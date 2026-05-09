---
name: pr-review
description: Use when the user wants to read a PR's diff, run a code-review pass with fresh context, and action the findings as commits on the same branch. Designed as a chain step — does not spawn sub-agents (the chain runner already gives fresh context). Trigger phrases - "/pr-review", "review and action this PR".
category: dev
---

# PR Review

Read a PR's diff, run a code-review pass, and action the findings on the same branch. The chain runner provides fresh context — no sub-agents inside this skill.

## Inputs

```json
{ "pr": 123, "branch": "agent/issue-582-foo" }
```

When invoked without context, detect the PR from the current branch:

```bash
gh pr view --json number,title,body,headRefName,baseRefName,closingIssuesReferences
```

## Workflow

### Step 1 — Gather PR context

```bash
gh pr view <pr> --json number,title,body,headRefName,baseRefName,closingIssuesReferences
gh pr diff <pr>
```

For each entry in `closingIssuesReferences`, fetch the issue body:

```bash
gh issue view <N> --json title,body,labels
```

The acceptance criteria from the linked issue drive Step 2's coverage check.

### Step 2 — Metadata + scope check

Validate the following. Each becomes a finding if it fails.

| Check | Criteria | Severity if failing |
|---|---|---|
| Title | Descriptive, explains what changed | Low |
| Description | Present, explains what + why, links the issue | Low |
| Single concern | Diff is scoped to one logical change | Medium |
| Out-of-scope changes | No unrelated files / hunks | Medium |
| Acceptance criteria | Each criterion from the linked issue is visibly addressed | High |

Load `references/pr-metadata-checklist.md` for the full rubric.

### Step 3 — Code-review pass

Read the diff and apply the review heuristics. Focus on:

- **Correctness** — wrong logic, wrong type, off-by-one, dropped error handling
- **Project conventions** — naming, file layout, import order, framework patterns from `AGENTS.md` / `standards-*` skills if installed
- **Performance** — obvious N+1, sync work in hot paths, unnecessary allocations
- **Test coverage** — every acceptance criterion has a behaviour test; tests verify through public interfaces
- **Readability** — dead code, redundant branches, premature abstraction, unhelpful comments

For each finding, classify by severity (rubric in `references/review-severity-rubric.md`):

- **CRITICAL** — production crash, data loss, unaddressed acceptance criterion
- **HIGH** — significant bug, missing error handling on a sensitive path, breaking change without migration
- **MEDIUM** — code-quality issue, test gap, minor breaking change
- **LOW** — style, trivial nits

### Step 4 — Action findings

For every **Critical**, **High**, and **Medium** finding: **fix the code in this run.** Do not defer.

For **Low** findings: fix if trivial. Defer only if genuinely out of scope; create a tracking issue in that case:

```bash
gh issue create \
  --title "chore: <short description>" \
  --body "Deferred from PR #<N> review — accepted as-is because <reason>." \
  --label "planned,afk"
```

### Step 5 — Commit and push

Group fixes by concern. One commit per logical change.

```bash
git add <files>
git commit -m "Address review: <one-line summary>"
git push origin HEAD
```

### Step 6 — Post a single review-summary comment

```bash
gh pr comment <pr> --body "$(cat <<EOF
## Automated review

### Fixed
- [list each finding actioned with file:line]

### Deferred (Low only — Critical/High/Medium are always fixed)
- [item — reason — tracked in #N]
EOF
)"
```

### Step 7 — Report

```json
{ "pr": <N>, "findings": <count>, "fixed": <count>, "deferred": <count> }
```

## Constraints

### MUST DO
- Read the diff and linked issue body before flagging anything.
- Action every Critical / High / Medium finding in the same run — do not defer them.
- Track deferred Low findings as GitHub issues; never silently drop.
- Commit fixes incrementally, grouped by concern.

### MUST NOT DO
- Spawn sub-agents. The chain runner already provides fresh context.
- Run the quality gate. The chain runner inserts `quality-gate` as the next step.
- Force-push.
- Resolve merge conflicts (`merge-main` is a separate label-driven trigger).
- Merge the PR.
- Post review comments without first committing the fixes.
