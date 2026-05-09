---
name: pr-wait-checks
description: Use when the user wants to wait for a pull request's CI checks to complete before continuing. Polls `gh pr checks` until every check has finished (passed or failed) or a timeout elapses, then reports the rollup. Trigger phrases - "/pr-wait-checks", "wait for checks", "wait for ci".
category: dev
---

# PR Wait for Checks

Wait until a pull request's CI checks have all reached a terminal state. Reports the conclusion so the next caller (a review skill, a human, or an orchestrator) can react to actual results rather than a half-known state.

## Inputs

When invoked with arguments, the first line of the prompt may carry a context envelope as JSON:

```json
{ "pr": 123, "branch": "agent/issue-582-foo", "timeoutSecs": 1200 }
```

When invoked without context, infer `pr` from `gh pr view --json number` against the current branch. `timeoutSecs` defaults to 1200 (20 minutes) — long enough for most projects' CI matrices.

## Workflow

### Step 1 — Confirm the PR has at least one check defined

```bash
gh pr checks <pr> --json name,bucket,state
```

If the output is `[]`, the repo has no CI configured for this PR. Report `no-checks` and exit successfully — the caller can decide whether that's acceptable.

### Step 2 — Poll until every check is terminal

A check is **terminal** when its `bucket` is `pass`, `fail`, `skipping`, or `cancel`. Anything else (`pending`, `running`) means the run is still in flight.

Poll every 30 seconds. Stop when:

1. **All terminal** — every row's bucket is in the terminal set. Move to Step 3.
2. **Timeout** — wall-clock time exceeded `timeoutSecs`. Report `timeout` with the latest snapshot.

Use the `gh` CLI:

```bash
gh pr checks <pr> --json name,bucket,state,conclusion
```

Don't use `gh pr checks --watch` — it has its own internal logic and can hang past timeouts. A simple poll loop with a deadline is more predictable.

### Step 3 — Compute the rollup

- **`pass`** — every check has `bucket: pass` (or `skipping`)
- **`fail`** — at least one check has `bucket: fail`
- **`mixed`** — anything else terminal (e.g. some skipped, some passed) — treat as `pass` for downstream gating but call it out in the report

### Step 4 — Report

```json
{
  "pr": <N>,
  "rollup": "pass" | "fail" | "no-checks" | "timeout",
  "checks": [
    { "name": "build", "bucket": "pass" },
    { "name": "test", "bucket": "fail", "conclusion": "failure" }
  ],
  "elapsedSecs": <int>
}
```

If `rollup === "fail"`, also print a one-line summary listing which check(s) failed so a human (or the next chain step) can read the log without re-running `gh pr checks`.

## Constraints

### MUST DO
- Treat `pending` and `running` as in-flight; only the explicitly-terminal buckets stop the poll.
- Honour `timeoutSecs` strictly. A wedged CI shouldn't pin a chain forever.
- Report enough detail (`checks[]`) that the next caller can decide what to do without re-fetching.

### MUST NOT DO
- Push commits or make any code changes — this is a read-only wait.
- Use `gh pr checks --watch` — its blocking semantics fight the timeout contract.
- Skip the `no-checks` early-return — silently waiting forever for checks that don't exist looks like a hang.
- Trigger or re-run checks. If a check looks stuck, report it; let a human or another skill act on the result.
