---
name: code-review-and-quality
description: Use when reviewing any pull request or code change before merge. Use for five-axis evaluation of correctness, readability, architecture, security, and performance. Use as the primary skill backing the /review command.
---

## Core Workflow

1. **Fetch the PR** — `gh pr view <N>` and `gh pr diff <N>`. These are the source of truth.
2. **Fetch linked issues** — `gh issue view <N>` for each `Closes #N`. AC drive the review.
3. **Run the five-axis review** — for each changed file, evaluate:

### Axis 1: Correctness
- Wrong logic, wrong type, off-by-one, dropped error handling
- Broken invariants, unhandled edge cases
- All acceptance criteria visibly addressed

### Axis 2: Readability
- Dead code, redundant branches, premature abstraction
- Unhelpful comments vs. missing why-comments
- Naming clarity — does the name reveal intent?

### Axis 3: Architecture
- Module boundaries — are they justified? Deletion test applies.
- AHA violations — abstracted before the third occurrence
- Single-adapter seams with unnecessary interfaces

### Axis 4: Security
- Input validation on external boundaries (SQL, shell, file paths, HTML)
- Auth/authz — new endpoints have correct guards
- Secrets — no hardcoded keys or tokens
- Data exposure — PII in logs, debug endpoints, error messages
- Supply chain — new dependencies vetted?

### Axis 5: Performance
- N+1 queries, sync work in hot paths
- Unnecessary allocations, unbounded loops
- Bundle impact for new UI imports

4. **Classify findings** — assign severity:

| Severity | Action |
|---|---|
| Critical | Production crash, data loss, unaddressed AC, broken CI |
| High | Significant bug, missing error handling, breaking change without migration |
| Medium | Code quality, test gap, minor breaking change |
| Low | Style, naming, minor nit |

5. **Fix Critical/High/Medium findings** — commit grouped by concern.
6. **Defer Low findings** — create tracking GitHub issues.
7. **Post review comment** — summary of fixes, deferred items, AC coverage.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll fix the style issues later" | They never get fixed. File issues or fix now. |
| "It's not my code, I shouldn't rewrite it" | You're reviewing the change, not the author. Flag problems in the diff. |
| "The tests pass, so it must be correct" | Tests only prove what they assert. Missing test coverage is a finding. |
| "This is an edge case, no one will hit it" | Edge cases in production are crashes. Fix them. |

## Red Flags

- [ ] Review only checks correctness and ignores architecture
- [ ] Findings are filed without severity labels
- [ ] Low findings are silently dropped instead of tracked
- [ ] The reviewer didn't read the linked issue or acceptance criteria
- [ ] No comment posted on PR with zero findings ("no news is good news" is insufficient)

## Verification

- [ ] All Critical/High/Medium findings are fixed and committed
- [ ] Low findings have tracking issues
- [ ] Review comment posted on the PR
- [ ] Every acceptance criterion from linked issues is covered
- [ ] No security-relevant findings are deferred

## Constraints

### MUST DO
- Fetch PR metadata and diff via `gh` — never rely on the PR description alone.
- Fetch linked issue details for each `Closes #N`.
- Action every Critical/High/Medium finding in the same run.
- Track deferred Low findings as GitHub issues.
- Post the review comment on every run — even with zero findings.

### MUST NOT DO
- Spawn sub-agents — run in a single context.
- Run lint, typecheck, tests, or build — pre-push hooks enforce those.
- Force-push, resolve merge conflicts, or merge the PR.
- Defer Critical/High/Medium findings — always fix them now.
