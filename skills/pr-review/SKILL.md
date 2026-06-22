---
name: pr-review
description: Automated PR review producing a `## Automated review` comment, applying risk label and `reviewed` on completion. Use when reviewing a pull request — fetches PR metadata and linked issue ACs, traces data flow, verifies AC coverage, classifies findings by exposure, and posts findings via gh pr comment.
category: review
public: false
---

# PR Review

Single reasoning pass that produces (a) a `## Automated review` comment with findings and AC coverage, (b) a `risk-low`/`risk-medium`/`risk-high` label that decides whether humans must review, and (c) a `reviewed` label on completion.

## Inputs

The skill accepts an optional variables block. CI mode appends one after the slash command; human mode omits it. The skill must handle both.

### Variable block (CI mode)

```
--- VARIABLES ---
PR_NUMBER=<pr number>
PR_CONTEXT_FILE=<path to pr-context.json>
LINKED_ISSUES_FILE=<path to linked-issues.json>
```

When the block is present, read the values directly. The workflow has already fetched the JSON files — don't re-fetch.

### Resolving variables (human mode)

When the variables block is absent (human invocation):

1. Run `gh pr view --json number,headRefName,baseRefName` against the current branch. If a PR is open, use its number.
2. If no PR is open, ask the human which PR to review.
3. Fetch PR metadata with `gh pr view $PR_NUMBER --json title,body,closingIssuesReferences`.
4. For each linked issue, fetch with `gh issue view <N>`.
5. Skip `PR_CONTEXT_FILE` and `LINKED_ISSUES_FILE` — fetch everything via `gh` directly.

### Mode detection

A `--- VARIABLES ---` marker line in the prompt indicates CI mode. Absence indicates human mode.

### Interaction rules

- **CI mode** — never ask for confirmation. The review is non-interactive.
- **Human mode** — ask only when genuinely ambiguous (no PR on current branch, multiple PRs match, etc.). Otherwise proceed and post the comment.

### Fetch order (both modes)

1. PR title, body, base/head refs (from `PR_CONTEXT_FILE` in CI; from `gh pr view` in human mode).
2. Linked issue bodies (from `LINKED_ISSUES_FILE` in CI; from `gh issue view` in human mode).
3. Diff via `gh pr diff $PR_NUMBER`.

## Reasoning model

Five steps. Run them in order. Skip none.

### 1. Build a mental model

Read enough surrounding code to answer: what system does this diff touch and what role does it play? Read the file the diff modifies, plus its public callers, plus the test file. _Do not_ skim — the diff hunks alone don't reveal intent.

### 2. Trace changed paths

For each file in the diff, identify the trace: entry → action → service → sink. Read the full path, not just the diff hunk. A missing `try/catch` that the caller already provides is not a finding. A `console.log` of an unredacted value is.

### 3. State the strongest version

For each candidate finding, write the strongest case against the change. If the strongest case is weak, drop it. Findings survive only when the strongest version is concrete.

### 4. Verify the ACs

Read each linked issue's acceptance criteria. For each AC, locate the diff line(s) that implement it; identify the test that proves it works through a public interface. Flag "AC not addressed" or "AC test gap" as findings. See `references/intent-check.md`.

### 5. Classify by exposure × impact

Replace the rigid 4-level table with two axes. See `references/severity-classification.md`.

- **Exposure** — is the path reachable in production? Dead code with a bug is low exposure; the same bug on `/api/v1/posts` is high exposure.
- **Impact** — what's the consequence? Crash, data loss, security boundary breach, observability hole, test gap, lint nit.

A finding is one of: `critical` (reachable + severe), `high` (reachable + significant), `medium` (reachable + moderate, or unreachable + significant), `low` (anything else). Drop findings that are neither reachable nor significant.

## Single-finding bar

Every reported finding must include: **file:line** + **trace** (entry → sink) + **concrete failure or exploit path**. Findings that don't meet this bar get dropped. No exceptions.

This is the discipline that kills checklist-mimicry. If you can't write the trace, you don't have a finding.

## Categorization (drives the label)

Compute the PR's risk label. Three dimensions, summed into a single label.

| Dimension              | What to score                                                                                                                  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Blast radius**       | Count of production modules touched. Sensitive systems (auth, payments, scheduling, data deletion, encryption) are floor High. |
| **Reversibility**      | Pure additive → -1 level. One-way (data backfill, schema change, drop column) → +1 level.                                      |
| **Invariant breakage** | Signature changes, schema changes, env changes, error-semantic flips, authz check changes. Each is +1 level.                   |

Take the highest floor; apply reversibility and invariant adjustments. Output the level with a one-sentence justification. See `references/categorization.md`.

Labels and merge semantics:

- **`risk-low`** — agent-only review. Comment + label. No human review required.
- **`risk-medium`** — comment + label. Human reviews the agent's flagged concerns, then merges.
- **`risk-high`** — comment + label. Full human review of the diff is mandatory.

## Comment template

Always this exact structure — `## Automated review`, `### Findings`, `### Acceptance criteria coverage`, `### Security`, `### Risk`. No findings → fill with `None.` / `All criteria addressed.` / `No security-relevant changes.` See `references/comment-template.md`.

## Action findings

Fix every finding by editing code in this run. Commit grouped by concern:

```bash
git add <files>
git commit -m "Address review: <one-line summary>"
# For security: git commit -m "Security review: <one-line summary>"
```



## Push + label workflow

```bash
# Push any committed fixes (independent step — failures here must not block the comment).
if git log --oneline origin/HEAD..HEAD 2>/dev/null | grep -q .; then
    git push origin HEAD || true
fi

# Post the comment (this is the contract — must run regardless of push outcome).
gh pr comment <N> --body "$(cat <<'EOF'
## Automated review

### Findings
- [severity | file:line | trace → impact] — fixed in commit <sha>
- ...

### Acceptance criteria coverage
- [criterion verbatim] — covered by `<test-file>:<test-name>`
- [criterion verbatim] — **NOT COVERED** — see #N

### Security
- [severity | file:line | trace → impact] — fixed in commit <sha>
- (No security-relevant changes.)

### Risk
<low|medium|high> — <one-sentence justification>
EOF
)"

# Apply labels (each independent — never let one failure block the next).
gh pr edit <N> --remove-label reviewing --add-label reviewed || true
gh pr edit <N> --add-label "risk-<low|medium|high>" || true
```

## Reference guide

| File                                    | When to load                                     |
| --------------------------------------- | ------------------------------------------------ |
| `references/categorization.md`          | When deciding the risk label                     |
| `references/severity-classification.md` | When classifying a finding's severity            |
| `references/intent-check.md`            | When verifying AC coverage from the linked issue |
| `references/security-data-flow.md`      | When security findings are possible              |
| `references/comment-template.md`        | When composing the `## Automated review` comment |

## Constraints

### MUST DO

- Fetch PR metadata from `PR_CONTEXT_FILE` if present (CI mode) or via `gh pr view` (human mode) — these are the source of truth.
- Fetch linked issue details from `LINKED_ISSUES_FILE` if present (CI mode) or via `gh issue view <N>` (human mode) for each `Closes #N`.
- Action every finding in the same run — nothing is deferred.
- Trace data flow from external input to sensitive sink before declaring exploitability.
- **Post the review comment on every run, even with zero findings.** No comment means no signal.
- Apply the `risk-<level>` label and transition `reviewing` → `reviewed` after the comment posts.
- Comment MUST use the exact template structure from `references/comment-template.md`.
- In CI mode (variables block present): never ask for confirmation.
- In human mode (no variables block): ask only when genuinely ambiguous.

### MUST NOT DO

- Spawn sub-agents — run in a single context.
- Run lint, type-check, tests, or build — pre-push hooks enforce those.
- Run commands to reproduce vulnerabilities — read code only.
- Force-push.
- Resolve merge conflicts.
- Merge the PR.
- Post review comments without first committing the fixes.
- Report a finding you cannot trace to file:line.

## Do NOT flag

- DoS / rate limiting / resource exhaustion.
- Memory safety in memory-safe languages.
- XSS in React/Angular/Vue outside the framework escape hatches (`references/security-data-flow.md` handles these with proper exploit-path analysis).
- Regex injection / regex DoS.
- Missing audit logs unless they violate an acceptance criterion.
- Issues in documentation or unit-test-only files.
- Issues requiring control of env vars or CLI flags.
