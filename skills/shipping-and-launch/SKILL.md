---
name: shipping-and-launch
description: Use when preparing to ship a feature, release, or deployment. Use before merging to main, creating a release, or deploying to production. Do NOT use for CI-only changes or internal refactors that don't reach production.
---

## Core Workflow

### Phase 1 — Pre-launch checklist

Run these checks in parallel:

- [ ] **Code review** — invoke the code-reviewer agent for a five-axis review
- [ ] **Security audit** — invoke the security-auditor agent for a threat model review
- [ ] **Test verification** — invoke the test-engineer agent to verify test coverage and acceptance criteria
- [ ] **Quality gate** — lint → typecheck → test → build, all pass
- [ ] **Migration safety** — irreversible migrations are backward-compatible; rollback script exists
- [ ] **Feature flags** — new code is behind a flag if rollout risk is non-zero
- [ ] **Observability** — meaningful logs on user-visible state changes; error paths are instrumented
- [ ] **Documentation** — relevant docs/README/API docs updated; changelog entry added
- [ ] **Dependency surface** — no new transitive deps added without review; lockfile changes intentional

### Phase 2 — Staged rollout

1. **Canary** — deploy to a subset of users or environments. Monitor for 15+ minutes.
2. **Gradual** — increase to 25% → 50% → 100%. Watch error rates and latency at each step.
3. **Full** — roll out to 100%. Verify in production monitor.

### Phase 3 — Rollback readiness

- Rollback script is tested and works (revert the PR, migrate down, re-deploy).
- Rollback decision criteria are defined: error rate > X%, latency > Yms, P1 bug confirmed.
- Rollback is faster than fix-forward for data-affecting changes.

### Phase 4 — Post-launch

- Monitor for 1 hour after full rollout.
- If stable, remove feature flag (if added).
- Close the tracking issue with a summary of the rollout.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "It's just a small change, I don't need a checklist" | Small changes cause the most production incidents — they skip the process. |
| "I'll add observability after launch" | You'll add it never. Production problems without observability are invisible. |
| "Feature flags are overkill for this" | Feature flags are the cheapest rollback mechanism. No flag = no rollback without a new deploy. |
| "The tests pass, ship it" | Tests passing is table stakes. Did you check migrations, observability, docs, and rollback? |
| "I'll roll forward instead of rolling back" | Rollbacks are faster and safer. Roll forward only for non-data-affecting changes. |

## Red Flags

- [ ] No rollback plan exists for this change
- [ ] The change modifies the database without a reversible migration
- [ ] No observability (logs, metrics) on the new code paths
- [ ] The change is deployed directly to production without staging/canary
- [ ] The linked documentation is out of date

## Verification

- [ ] All pre-launch checks pass (review, security, test, quality gate)
- [ ] Rollback script is tested and proven
- [ ] Rollout has staged percentages (canary → gradual → full)
- [ ] Observability is confirmed in production monitor
- [ ] Post-launch monitoring is scheduled
- [ ] Feature flag is removed after stable period

## Constraints

### MUST DO
- Run all pre-launch checks before merging to main.
- Define rollback criteria before deploying.
- Use staged rollout for any change that could affect users.
- Verify observability before declaring launch complete.

### MUST NOT DO
- Deploy directly to production without staging.
- Skip rollback planning for data-affecting changes.
- Remove feature flags before the stable window expires.
- Launch without post-launch monitoring scheduled.

## Reference Guide

| File | When to load |
|------|-------------|
| `references/pre-launch-checklist.md` | During Phase 1 — full detailed checklist |
