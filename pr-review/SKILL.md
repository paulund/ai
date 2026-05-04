---
name: pr-review
description: Consolidated pull-request review workflow. Run a full review pass covering metadata, acceptance criteria, code quality, security, breaking changes, and documentation. Use when reviewing a PR, when asked to review code, or when a PR needs a comprehensive review. Trigger phrases - "/pr-review", "review this PR".
category: dev
---

# PR Review

Run a consolidated review on a single pull request: validate metadata and scope, verify acceptance criteria, and spawn parallel review passes for code quality, security, breaking changes, and documentation staleness. Present a unified, deduplicated report with severity-ranked findings, then draft review comments and ask before posting.

## Philosophy

- **Context is everything.** Reviews run in sub-agents with fresh context so implementation bias does not creep into the findings.
- **No PR is too small for a scope check.** Even a one-liner should explain why it exists and what ticket it closes.
- **Deduplicate before presenting.** Multiple agents may flag the same issue; the human reviewer only needs to see it once.
- **Comments are human-curated.** Draft them, but never post without explicit confirmation.

## Workflow

### Step 1 — Resolve the PR

Extract the PR number from the user's args. If none provided:

1. Query PRs awaiting your review:
   ```bash
   gh pr list --search "review-requested:@me" --state open --json number,title,author,url
   ```
2. If results exist, present them and ask the user to pick one (or auto-select if exactly one).
3. If no review-requested PRs exist, list all open PRs:
   ```bash
   gh pr list --state open --json number,title,author,url
   ```
4. If still none, stop and report: *"No open PRs found."*

### Step 2 — Gather PR metadata and linked ticket

Fetch PR details:
```bash
gh pr view <N> --json number,title,body,headRefName,baseRefName,author,url,closingIssuesReferences
```

- Parse `closingIssuesReferences` for linked issues.
- For each linked issue, run `gh issue view <N>` to extract the body and acceptance criteria.
- If **no linked issue exists**, flag as a metadata finding: *"PR has no linked issue — acceptance criteria cannot be verified."*

### Step 3 — PR metadata and scope check

Validate the following. Produce a pass/flag report.

| Check | Criteria |
|-------|----------|
| **Title** | Present, descriptive, and explains *what* the change does. |
| **Description** | Present, not empty or auto-generated, and explains *what* and *why*. |
| **Single concern** | Changes are scoped to one logical concern. Cross-domain changes are flagged. |
| **Out-of-scope changes** | No files or hunks appear unrelated to the PR's stated purpose. |
| **Acceptance criteria** | Each criterion from the linked issue is visibly addressed in the diff or PR body. |

Load `references/pr-metadata-checklist.md` for the full checklist and guidance.

### Step 4 — Run review passes in parallel (sub-agents)

Spawn sub-agents with **fresh context** so they review the diff without implementation bias. Run them in parallel.

**Agent A — Code Review**
- Load `~/.agents/skills/dev-review/SKILL.md` (or equivalent global skill path)
- Run against PR `#<N>`
- Return findings as: `severity | file:line | category | description | suggested fix`

**Agent B — Security Review**
- Load `~/.agents/skills/dev-security-review/SKILL.md` (or equivalent global skill path)
- Run against PR `#<N>`
- Return findings per that skill's markdown format

**Agent C — Breaking Changes**
- Prompt from `references/sub-agent-prompts.md`
- Analyze the diff for backward-incompatible changes: public API signatures, database migrations, configuration changes, removed endpoints, altered default behavior
- Return findings with migration guidance where applicable

**Agent D — Documentation Staleness**
- Prompt from `references/sub-agent-prompts.md`
- Check if README, CHANGELOG, API docs, inline docblocks, or other docs reference changed code and are now stale or missing updates
- Return findings per file

**Framework standards**
- Do NOT auto-detect frameworks or spawn framework-specific agents. The `dev-review` agent will naturally apply whatever global `standards-*` skills the user's project has installed.

### Step 5 — Deduplicate and consolidate feedback

Collect all sub-agent reports.

1. **Deduplicate** — merge findings that hit the same file/line/issue across multiple agents. Combine rationale and cite all contributing agents.
2. **Classify severity** — for each consolidated finding, assign one of:
   - **CRITICAL** — security RCE/auth bypass, data loss, production crash, unaddressed acceptance criteria
   - **HIGH** — significant bugs, missing error handling, security issues in sensitive paths, breaking changes without migration path
   - **MEDIUM** — code quality, maintainability, test gaps, minor breaking changes with migration path
   - **LOW** — style nits, minor documentation gaps, trivial optimizations

Load `references/review-severity-rubric.md` for detailed classification rules.

### Step 6 — Present findings

Render a single consolidated markdown report:

1. **Metadata & Scope Check** — pass/flag for each check from Step 3
2. **Acceptance Criteria Coverage** — linked issue(s), criteria status (covered / unaddressed / not applicable)
3. **Findings by Severity**
   - CRITICAL
   - HIGH
   - MEDIUM
   - LOW

Each finding must include: severity badge, file:line, category, description, suggested fix.

### Step 7 — Draft and optionally post PR comments

1. For each new finding (not already present as an existing review comment on the PR), draft a concise review comment.
2. Ask the user:
   > "Post these as pending review comments on PR #<N>? (y/n)"
3. If **yes**, post via:
   ```bash
   gh pr review <N> --comment --body-file <draft-file>
   ```
   Or equivalent `gh` commands for individual line comments where supported.
4. If **no**, present the drafted comments as copy-pasteable markdown.

## Constraints

### MUST DO
- Resolve the PR before reviewing.
- Check for linked issues and acceptance criteria.
- Run review passes in parallel sub-agents with fresh context.
- Deduplicate findings from multiple sub-agents.
- Classify all findings by severity.
- Ask the user before posting comments to the PR.

### MUST NOT DO
- Post comments to the PR without explicit user confirmation.
- Skip the metadata and scope check.
- Merge the PR.
- Run review passes in the same context as the implementation (always spawn fresh sub-agents).
- Flag issues in test files or documentation as security vulnerabilities (respect `dev-security-review` exclusions).
