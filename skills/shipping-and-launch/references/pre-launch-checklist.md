# Pre-Launch Checklist

Detailed expansion of the Phase 1 checks from the shipping-and-launch skill.

## Code Quality

- [ ] PR has been reviewed (five-axis: correctness, readability, architecture, security, performance)
- [ ] All Critical/High/Medium findings from review are fixed and committed
- [ ] No Critical/High security findings remain open
- [ ] Code follows project conventions (see standards-* skills)
- [ ] No debug code, console.logs, or TODO comments left behind
- [ ] Dead code and unused imports removed

## Test Verification

- [ ] Every acceptance criterion has a passing behaviour test
- [ ] Tests run in CI (or ran locally with the same config)
- [ ] No skipped or ignored tests
- [ ] Bug fixes include regression tests
- [ ] Test coverage has not decreased compared to the base branch

## Quality Gate

- [ ] Lint passes with zero warnings
- [ ] Typecheck passes with zero errors
- [ ] Test suite passes with zero failures
- [ ] Build completes successfully

## Migration Safety

- [ ] Migrations are backward-compatible (old code can read new schema, new code can read old schema)
- [ ] Destructive migrations have a rollout plan (expand → contract, not break → fix)
- [ ] Rollback script exists and has been tested
- [ ] Data backfills are idempotent and resumable
- [ ] No `DROP COLUMN`, `DROP TABLE`, or `ALTER TYPE` without a migration plan

## Feature Flags

- [ ] New code is behind a flag if rollout risk is non-zero
- [ ] Flag defaults to OFF in production
- [ ] Flag can be toggled without a redeploy (if using a flag service)
- [ ] Flag removal is tracked as a follow-up issue

## Observability

- [ ] Structured logs on all user-visible state changes
- [ ] Error paths are instrumented (log the error, not just the stack trace)
- [ ] RED metrics (Rate, Errors, Duration) on every new endpoint
- [ ] Correlation/request ID propagates end-to-end
- [ ] Alerts defined on user-felt symptoms (error rate, latency), not infrastructure causes
- [ ] No secrets, tokens, or PII in any log line
- [ ] Telemetry verified in staging (trigger the path, find the log line, confirm metrics appear)

## Security

- [ ] All user input validated at system boundaries
- [ ] Authorisation checked on every new endpoint (not just authentication)
- [ ] No secrets in source code or git history
- [ ] Security headers configured (CSP, HSTS, X-Frame-Options)
- [ ] Rate limiting on authentication endpoints
- [ ] `npm audit` (or equivalent) shows no critical or high vulnerabilities
- [ ] LLM output treated as untrusted (if AI features present)

## Documentation

- [ ] README updated if setup, config, or behaviour changed
- [ ] ADR written for significant architectural decisions
- [ ] Changelog entry added
- [ ] API documentation updated (OpenAPI spec if applicable)
- [ ] Environment variable changes documented in `.env.example`

## Dependency Surface

- [ ] No new transitive dependencies without review
- [ ] Lockfile changes are intentional
- [ ] New packages vetted for maintenance status, license, and known CVEs
- [ ] No typosquat or postinstall-script risks in new dependencies

## Rollback Readiness

- [ ] Rollback trigger conditions defined (error rate > X%, latency > Yms, P1 bug confirmed)
- [ ] Rollback steps are documented and tested
- [ ] Recovery time objective (RTO) is acceptable for this change
- [ ] Rollback is faster than fix-forward for data-affecting changes
- [ ] Someone is available to execute the rollback during the rollout window