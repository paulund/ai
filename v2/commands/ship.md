---
description: Ship to production — pre-launch checklist, parallel persona fan-out, staged rollout, rollback plan.
---

Invoke the `shipping-and-launch` skill.

## Workflow

1. **Parallel checks** — fan out to three agents simultaneously:
   - `code-reviewer` — five-axis code review
   - `security-auditor` — threat model review
   - `test-engineer` — test coverage and acceptance criteria verification
2. **Quality gate** — run `quality-gate` (lint → typecheck → test → build).
3. **Merge findings** — triage all findings across all lenses. Fix Critical/High/Medium. Defer Low.
4. **Staged rollout** — deploy canary → 25% → 50% → 100%.
5. **Rollback plan** — rollback script tested and ready. Decision criteria defined.
6. **Post-launch** — monitor for 1 hour. Close issue with rollout summary.

## Constraints

- Do not ship if any Critical/High findings remain unaddressed.
- Do not skip staged rollout for production-affecting changes.
- Do not ship without a tested rollback plan for data-affecting changes.
