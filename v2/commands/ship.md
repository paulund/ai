---
description: Ship to production — pre-launch checklist, parallel persona fan-out, staged rollout, rollback plan.
---

Invoke the `shipping-and-launch` skill.

## Workflow

1. **Parallel checks** — fan out to three agents simultaneously:
   - `code-reviewer` — five-axis code review
   - `security-auditor` — threat model review
   - `test-engineer` — test coverage and acceptance criteria verification
2. **Merge findings** — in the main context (not a persona), synthesize all three reports:
   - **Code quality** — aggregate Critical/Important findings from `code-reviewer`; resolve duplicates.
   - **Security** — promote any Critical/High `security-auditor` findings to launch blockers.
   - **Observability** — apply the `observability-and-instrumentation` skill: confirm structured logs with correlation IDs, RED metrics on new endpoints, alerts on symptoms (not causes). Any new feature touching external calls or queues with zero telemetry is a launch blocker.
   - **Accessibility** — verify keyboard nav, screen reader support, contrast. Handle directly here; none of the three personas cover this.
   - **Infrastructure** — env vars, migrations, feature flags. Verify directly.
   - **Documentation** — README, ADRs, changelog. Verify directly.
3. **Quality gate** — run `quality-gate` (lint → typecheck → test → build).
4. **Fix Critical/High/Medium findings** — commit grouped by concern. Defer Low.
5. **Rollback plan** — define rollback trigger conditions, exact steps, and recovery time objective. This is mandatory before any GO decision.
6. **Staged rollout** — deploy canary → 25% → 50% → 100%.
7. **Post-launch** — monitor for 1 hour. Close issue with rollout summary.

## Skip the fan-out only when ALL of the following are true

- The change touches 2 files or fewer
- The diff is under 50 lines
- It does not touch auth, payments, data access, or config/env

Otherwise, default to fan-out even if the diff looks small.

## Persona resolution

If you've defined your own `code-reviewer`, `security-auditor`, or `test-engineer` in `agents/` or your global configuration, those take precedence over any defaults. User-level definitions win by design.

## Constraints

- Do not ship if any Critical/High findings remain unaddressed.
- Do not skip staged rollout for production-affecting changes.
- Do not ship without a tested rollback plan for data-affecting changes.
- A rollback plan is required before a GO decision — never optional.

## Release

If the user also requests a GitHub release (or specifies a version/tag):

1. Run in parallel: `git fetch --tags`, `git tag --sort=-v:refname | head -5`, `git log <last-tag>..HEAD --oneline`.
2. Determine next semver — `feat:` → minor; `fix:`/`perf:` → patch; `BREAKING CHANGE` or `!` → major. Confirm version with user.
3. Write customer-facing release notes — brief summary + optional `## What's new`, `## Bug fixes`, `## Improvements`. Skip CI, deps, internal commits. Confirm notes with user.
4. Tag off `main`: `gh release create <version> --title "<version>" --notes "<notes>" --target main`
5. Print the published URL.
