---
description: Review a pull request — mode-aware (CI variables block or human). Fetches PR data and linked issues, runs the five-axis review with data-flow tracing, classifies findings by exposure × impact, computes the risk label, fixes Critical/High/Medium in-run, and posts the `## Automated review` comment.
---

# Review a PR

Provide a PR number or URL. The flow is the same in CI and human mode — only the inputs and which agents you may invoke differ.

## Mode detection

A `--- VARIABLES ---` block in the prompt indicates CI mode. Its absence indicates human mode. If both are present in some hybrid, treat as CI mode.

### CI mode (variables block present)

```
--- VARIABLES ---
PR_NUMBER=<pr number>
PR_CONTEXT_FILE=<path to pr-context.json>
LINKED_ISSUES_FILE=<path to linked-issues.json>
```

Read the values directly. The workflow has already fetched the JSON files — don't re-fetch.

### Human mode (no variables block)

1. Run `gh pr view --json number,headRefName,baseRefName` against the current branch. If a PR is open, use its number.
2. If no PR is open, ask the human which PR to review.
3. Fetch PR metadata with `gh pr view $PR_NUMBER --json title,body,closingIssuesReferences`.
4. For each linked issue, fetch with `gh issue view <N>`.
5. Skip `PR_CONTEXT_FILE` and `LINKED_ISSUES_FILE` — fetch everything via `gh` directly.

## Fetch (both modes)

1. PR title, body, base/head refs.
2. Linked issue bodies (one per `Closes #N`).
3. Diff via `gh pr diff $PR_NUMBER`.

## Review

1. **Invoke the `code-review-and-quality` skill.** It contains the five-axis review, exposure × impact severity model, AC coverage check, risk label computation, and the `## Automated review` comment template.
2. **Human mode only** — additionally invoke the `code-reviewer` and `security-auditor` agents to cross-check the skill's findings. CI mode runs in a single context, no sub-agents.

## Fix and comment

Follow the `code-review-and-quality` skill for fixing Critical/High/Medium findings, classifying Low as deferral, and posting the comment.

## Labels and transitions (CI mode only)

```bash
# Independent steps — never let one failure block the next.
gh pr edit <N> --remove-label reviewing --add-label reviewed || true
gh pr edit <N> --add-label "risk-<low|medium|high>" || true
```

## Constraints

### MUST DO

- Fetch PR metadata and diff via `gh` — never rely on the PR description alone.
- Fetch linked issue details for each `Closes #N`.
- Trace data flow (entry → action → service → sink) before declaring a finding's exploitability.
- Classify findings by **exposure × impact**, not a flat 4-level table.
- Post the `## Automated review` comment on every run — even with zero findings.
- Apply the `risk-<level>` label and transition `reviewing` → `reviewed` in CI mode.

### MUST NOT DO

- **CI mode** — spawn sub-agents. Run in a single context.
- Run lint, type-check, tests, or build — pre-push hooks enforce those.
- Force-push, resolve merge conflicts, or merge the PR.
- Defer Critical/High/Medium findings — always fix them now.
- Report a finding you cannot trace to file:line.
