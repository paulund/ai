---
name: incremental-implementation
description: Use when implementing any planned task, building a feature slice-by-slice, or working through a GitHub issue. Always use with test-driven-development and quality-gate. Do NOT use for research, exploration, or spike work.
---

## Core Workflow

1. **Read the task** — load the issue or task card. Acceptance criteria drive the TDD cycles. If AC are ambiguous, stop — do not guess.
2. **Module design check** — before writing code, verify:
   - Does this logic already exist? Extend, don't duplicate.
   - Apply the deletion test to any new module you're considering.
   - Identify the seam and public interface.
   - One adapter or two? Single adapter = call directly, no interface.
   - AHA check: have you seen this pattern 3 times? If not, duplicate.
3. **Implement one cycle** — for each acceptance criterion:
   - RED: write one test. Watch it fail for the right reason.
   - GREEN: minimum code to pass. No extras.
   - REFACTOR: clean up only if needed. Never refactor while RED.
4. **Run quality gate** — lint → typecheck → test → build. Stop on failure.
5. **Simplify** — remove dead code, flatten nesting, inline pass-through wrappers.
6. **Commit** — `git add -A && git commit -m "<imperative summary>" && git push`.
7. **Verify clean tree** — `git status --porcelain` must be empty.
8. **Repeat** — move to the next acceptance criterion. If all done, proceed to PR.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll write all the tests first" | Horizontal test-first violates thin slicing. Write one test, make it pass, repeat. |
| "This module might be useful later, I'll build it now" | Speculative generality is the root of all bloat. Wait for the need. |
| "I don't need to run tests, it's a small change" | Small changes cause regressions too. The gate exists for a reason. |
| "Let me refactor this related code while I'm here" | Scope creep. Touch only what the task requires — file a follow-up. |
| "I'll just skip the module design check, I know what I'm doing" | The check catches shallow modules, duplicate paths, and premature abstractions. Run it. |

## Red Flags

- [ ] You're writing code before checking if the logic already exists
- [ ] You're adding a port/interface for a seam with only one adapter
- [ ] You're abstracting on first use ("I'll need this later")
- [ ] You're fixing unrelated drive-by issues in the same branch
- [ ] Tests are passing but the working tree is dirty

## Verification

- [ ] Every acceptance criterion has a passing behaviour test
- [ ] Tests use public interfaces only — would survive an internal refactor
- [ ] `git status --porcelain` is empty
- [ ] All changes are committed and pushed
- [ ] Quality gate passed (lint → typecheck → test → build)

## Constraints

### MUST DO
- Run the module design check before writing any code.
- Write tests through public interfaces — never through internal helpers.
- Run `git status --porcelain` before reporting completion.
- Keep commits scoped to the current task.
- Run quality gate after every cycle.

### MUST NOT DO
- Extract a reusable abstraction on first use (AHA rule: wait for third occurrence).
- Introduce a port with only one concrete adapter.
- Fix drive-by issues unrelated to the task.
- Refactor while RED — get to GREEN first.
- Report success with uncommitted changes.
