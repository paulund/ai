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
