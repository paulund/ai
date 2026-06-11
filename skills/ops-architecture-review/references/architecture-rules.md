# Architecture Rules

Rules that define what counts as a "keep" vs "remove" candidate during the deletion test.

## Keep Rules (apply deletion test → keep if ANY match)

| Rule | Description |
|------|-------------|
| **Boundary enforcement** | Module enforces a critical system boundary (auth, payments, privacy, external API). Deleting it would leak concerns across the boundary. |
| **Non-trivial protocol** | Module implements a substantive protocol (state machine, retry logic, circuit breaker, consensus). The complexity is in the protocol, not the callers. |
| **Data integrity** | Module is the single location where a critical invariant is enforced (uniqueness, ordering, idempotency, referential integrity). |
| **Test surface** | Module provides a meaningful test seam — callers would be significantly harder to test without it. |
| **Multiple consumers** | Module has 3+ distinct callers with different needs. Inlining would duplicate logic. |
| **Unstable dependency** | Module wraps a dependency that changes frequently or has breaking changes (external API, unstable library). The wrapper absorbs churn. |

## Remove Rules (apply deletion test → remove if ANY match)

| Rule | Description |
|------|-------------|
| **Shallow wrapper** | Module is a thin pass-through (interface complexity ≈ implementation complexity). Deleting it concentrates complexity in callers without adding new complexity. |
| **Single consumer** | Module has exactly 1 caller. The abstraction adds indirection without sharing. |
| **Testability theatre** | Module was extracted "for testability" but tests still require the same setup, and real bugs occur in how callers use it (no locality of failure). |
| **Leaky abstraction** | Module's interface exposes its internal structure (returns internal types, requires caller to know implementation details). |
| **Premature abstraction** | Module was created for a future use case that never materialized. No evidence it will be reused. |
| **Dead code path** | Module contains code paths that are never exercised in production. |

## Mandatory Checks

1. **At least one Keep Rule must pass** for any candidate flagged as a deepening opportunity.
2. **If zero Keep Rules pass**, the module is a removal candidate — do not create a deepening issue.
3. **Reference the specific rule(s)** in the issue's "Current shape (shallow)" section.
