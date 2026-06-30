# Categorization — Computing the Risk Label

Three dimensions, summed into a single label that decides merge semantics.

## Dimension 1: Blast radius

Count the number of production modules the diff touches. Identify whether any sensitive system is touched.

| Touches                                                                                   | Risk floor                |
| ----------------------------------------------------------------------------------------- | ------------------------- |
| Pure docs, comments, formatting, types-only                                               | Low                       |
| Tests only (no production code changed)                                                   | Low                       |
| New isolated feature (no calls into existing modules, no schema)                          | Low–Medium                |
| Refactor of existing module (preserves external API)                                      | Medium                    |
| Touches >= 5 production modules                                                           | Medium floor              |
| Schema migration (Prisma schema or migrations/ folder)                                    | Medium +1 → High          |
| New external dependency in lockfile                                                       | Medium +1 → High          |
| **Sensitive system: auth, payments, scheduling, data deletion, encryption, key material** | High (regardless of size) |

## Dimension 2: Reversibility

| Operation                                                                                                        | Risk adjustment |
| ---------------------------------------------------------------------------------------------------------------- | --------------- |
| Pure additive (new files, new code paths, no existing logic modified)                                            | -1 level        |
| Modifies existing logic, easily revertible via git revert                                                        | no change       |
| One-way migration (data backfill, schema column drop, irreversible data transform)                               | +1 level        |
| Touches production infrastructure (env vars, cron schedules, deploy config, feature flags affecting all tenants) | +1 level        |

Reversibility adjustments cap at Medium→High. They cannot promote a Low-risk PR to High risk on their own — only irreversible changes that touch a sensitive system are High.

## Dimension 3: Invariant breakage

Other code may rely on these as assumptions. Read the callers of modified exports and look for breakage.

| Invariant change                                                                | Risk adjustment             |
| ------------------------------------------------------------------------------- | --------------------------- |
| Public function signature change (parameter added/removed, return type changed) | +1 level                    |
| Public type/exports change                                                      | +1 level                    |
| Prisma schema change (any model field added/removed/renamed)                    | +1 level                    |
| Env var or config key renamed/changed/removed                                   | +1 level                    |
| Error semantics flip (was swallowed → now propagated, or vice versa)            | +1 level                    |
| Async/timing change (sync → async, or vice versa)                               | +1 level                    |
| Authorization check added, removed, or modified                                 | → High (regardless of size) |

Multiple invariant changes accumulate: +2 invariants on a Medium floor = High.

## Combining

```
floor = max(blast_radius_floor, sensitive_system_floor)
level = floor
level += reversibility_adjustment (if applicable)
level += invariant_adjustment (count)
level = clamp(level, low, high)
```

The justification in the comment is one sentence explaining the dominant dimension — what tipped it into the chosen band.

## Examples

**PR: typo fix in a comment.**

- Blast radius: Low (docs-only).
- Reversibility: no change.
- Invariants: none.
- Result: `risk-low` — "Comment-only change, no code touched."

**PR: add a new isolated `/api/v1/foo` endpoint with tests.**

- Blast radius: Low–Medium (new module, public API).
- Reversibility: -1 (pure additive).
- Invariants: none.
- Result: `risk-low` — "New isolated endpoint, additive, fully tested."

**PR: refactor `refreshOAuthTokens` to use `getProviderRegistry()` (the PR I just shipped).**

- Blast radius: Medium (touches existing module, preserves external API).
- Reversibility: no change.
- Invariants: +1 (signature unchanged, but `createSocialProviderRegistry()` removed from public API).
- Result: `risk-medium` — "Refactor of existing module, preserves external API, one consumer was relying on the removed public symbol."

**PR: schema migration dropping a `RateLimitAttempt` column.**

- Blast radius: Medium +1 = High (schema migration).
- Reversibility: +1 (one-way).
- Invariants: +1 (schema change).
- Result: `risk-high` — "Schema migration is irreversible and breaks any external readers."

**PR: remove an auth check from `/api/v1/posts`.**

- Blast radius: any.
- Reversibility: any.
- Invariants: → High (authz check removed).
- Result: `risk-high` — "Authorization check removed from public endpoint."

## What this doesn't catch

- Cross-PR context: "this PR depends on #1234 landing first."
- Stakeholder tradeoffs: "this is technically fine but the team agreed to deprecate this approach."
- Performance regressions at production load: only visible at runtime.
- Visual/UX regressions: not visible in code review.

These remain human concerns. The label tells the human "this is the bucket of PRs where you can skim" vs "this is the bucket where you read carefully" vs "this is the bucket where you verify the agent's flagged concerns are real."
