---
name: quality-gate
description: Use when you need lint, typecheck, test, and build to pass before pushing, opening a PR, or merging. Also use when asked to run "checks", "quality gate", or "make sure everything is clean" before submission.
---

# Quality Gate

Run lint → typecheck → test → build. Stop on first failure, diagnose, fix, and re-run.

## Core Workflow

### Step 1 — Detect commands

Read `package.json` `scripts` (or `composer.json`, `Makefile`, `Cargo.toml`). Look for scripts named `lint`, `typecheck` / `types`, `test`, and `build`. Use `pnpm` if `pnpm-lock.yaml` exists, `npm run` otherwise. If a phase has no matching script, skip it.

### Step 2 — Run in order

```bash
<lint> && <typecheck> && <test> && <build>
```

Each must pass before the next runs.

### Step 3 — On failure, stop-the-line

1. **STOP** — do not run later phases.
2. **PRESERVE** full error output.
3. **DIAGNOSE** — reproduce if needed, trace root cause (`git log`, `git blame`, tests).
4. **FIX** the root cause.
5. **GUARD** — if the failure was a regression, add a test that would have caught it.
6. Re-run the full gate from Step 1.

Max **2 diagnose-fix cycles**. If still failing, surface the failure — don't hide it.

### Step 4 — Report

On success: one-line summary listing phases that ran.
On failure: failing phase, command that ran, and first ~20 lines of error output.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I don't need to run lint, I'll fix formatting later" | You'll fix it never. CI will fail. Run it now. |
| "The tests pass in isolation, I don't need the full suite" | Missing integration failures is how production bugs ship. Run the full gate. |
| "I'll skip typecheck, it takes too long" | Type errors caught in CI cost 10× more to fix than type errors caught locally. |
| "The build is fine, I already tested it" | Build failures happen from environment drift, cache invalidation, and dependency changes. Trust the rebuild. |
| "Just this once, I'll skip a phase" | One skipped phase becomes two, becomes all of them. Don't start. |

## Red Flags

- [ ] You're skipping a phase to "save time"
- [ ] You're not preserving the full error output
- [ ] You're on cycle 3+ and still failing
- [ ] You fixed the symptom, not the root cause
- [ ] You're editing configs or tests to mask the failure

## Verification

- [ ] Lint passes with zero warnings
- [ ] Typecheck passes with zero errors
- [ ] Test suite passes with zero failures
- [ ] Build completes successfully
- [ ] Regression test added for any failure caught

## Constraints

### MUST DO
- Detect toolchain from project files; do not assume `pnpm`.
- Run phases in order — fast feedback first.
- Surface failure output verbatim.
- Add a regression test when the failure is a regression.

### MUST NOT DO
- Skip a phase to save time.
- Run later phases after an earlier one failed.
- Loop more than 2 cycles.
- Mask failures by editing configs or tests without fixing the underlying issue.
