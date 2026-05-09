---
name: quality-gate
description: Use when the user wants to run the project's lint + types + build sequence as a gate before pushing, opening a PR, or merging. Invoked by chained dev skills between phases. Trigger phrases - "/quality-gate", "run the quality gate", "check it builds".
category: dev
---

# Quality Gate

Run the project's static-checks + build sequence. Stop on the first failure, diagnose the root cause, and fix it before re-running.

## Workflow

### Step 1 — Detect the toolchain

Read `package.json` `scripts` (or equivalent for the project: `composer.json`, `Makefile`, `Cargo.toml`). Pick the first command from each row that exists:

| Phase | Preferred | Fallback |
|---|---|---|
| Lint | `pnpm lint` / `npm run lint` | `npx next lint`, `composer run lint` |
| Types | `pnpm types` / `npm run types` | `npx tsc --noEmit` |
| Tests | `pnpm test` / `npm test` | `composer run test`, framework default |
| Build | `pnpm build` / `npm run build` | `npx next build` |

If a phase has no command for the project (e.g. a JS-only repo with no `types` script), skip that phase and continue.

### Step 2 — Run in order

Run the four phases sequentially. **Each must pass before the next runs.**

```bash
<lint command>
<types command>
<tests command>
<build command>
```

### Step 3 — On failure, Stop-the-Line

1. **STOP** running later phases.
2. **PRESERVE** the full error output — don't truncate it from your context.
3. **DIAGNOSE** — reproduce the failure if needed, trace the root cause, not the symptom. Use `git log` / `git blame` / existing tests for context.
4. **FIX** the root cause.
5. **GUARD** — if the failure was a regression, add a test that would have caught it.
6. Re-run the full gate from the top.

Max **2 diagnose-fix cycles**. If still failing, stop and surface the failure to the caller — don't hide it.

### Step 4 — Report

On success: one-line confirmation listing the phases that ran (`lint, types, tests, build`).
On failure: the failing phase, the command that ran, and the first ~20 lines of error output.

## Constraints

### MUST DO
- Detect toolchain commands from project files; do not assume `pnpm` everywhere.
- Run phases in the documented order — fast feedback first.
- Surface failure output verbatim to the caller; never silence it.
- Add a regression test on Step 3 step 5 when the failure is a regression.

### MUST NOT DO
- Skip a phase to "save time" — the gate is the contract.
- Run later phases after an earlier one failed.
- Loop more than 2 diagnose-fix cycles before stopping.
- Mask failures by editing the test or config to make them pass without fixing the underlying issue.
