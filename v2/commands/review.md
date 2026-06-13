---
description: Two-lens PR review — five-axis code review then security audit. Fixes Critical/High/Medium findings as commits, posts a single summary comment.
---

Invoke the `code-review-and-quality` skill.

Provide a PR number or URL. Fetch the PR metadata and diff via `gh pr view` and `gh pr diff`. Fetch linked issue details via `gh issue view`.

## Workflow

1. **General review** — run the five-axis review (correctness, readability, architecture, security, performance). The `code-reviewer` agent provides the review lens.
2. **Security audit** — run the `security-auditor` agent for a threat-model-focused review. Trace data flow from entry point to sensitive sink.
3. **Classify findings** — Critical, High, Medium, Low per the severity matrix.
4. **Fix Critical/High/Medium findings** — commit grouped by concern.
5. **Defer Low findings** — create tracking GitHub issues.
6. **Post review comment** — summary of fixes, deferred items, AC coverage.
