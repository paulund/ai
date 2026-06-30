---
name: documentation-and-adrs
description: Use when making significant architectural decisions, changing public APIs, or shipping features where future engineers and agents will need context to understand why things are built the way they are.
---

## Core Workflow

1. **Decide if an ADR is needed** — write one when the decision is:
   - Hard to reverse (framework, database, auth strategy, API architecture)
   - Surprising — the chosen option is not the obvious first choice
   - A genuine trade-off where alternatives were seriously considered

2. **Fill the ADR template** — save to `docs/adrs/ADR-NNN-<slug>.md`:
   ```
   # ADR-NNN: <Title>

   ## Status
   Accepted | Superseded by ADR-XXX | Deprecated

   ## Date
   YYYY-MM-DD

   ## Context
   What situation or constraint forced this decision?

   ## Decision
   What we chose and why.

   ## Alternatives Considered
   What was rejected and why — be specific.

   ## Consequences
   What becomes easier, what becomes harder, what we're betting on.
   ```

3. **Comment the why, not the what** — inline comments explain non-obvious intent, constraints, or known gotchas. Never restate what the code already says.

4. **README covers quick start** — every project needs: one-paragraph description, quick start steps, command table (dev / test / build / lint), architecture overview, contributing guide.

5. **Changelog for shipped features** — follow Keep a Changelog format: Added / Fixed / Changed / Removed sections per version.

6. **Agent context files stay current** — keep `AGENTS.md`, `CONTEXT.md`, and spec files updated. Agents use them to avoid re-deciding what's already been decided.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The code is self-documenting" | Code shows what. It doesn't show why, what alternatives were rejected, or what constraints apply. |
| "ADRs are overhead" | A 10-minute ADR prevents a 2-hour debate about the same decision 6 months later. |
| "Nobody reads docs" | Agents do. Future engineers do. Your 3-months-later self does. |
| "We'll write docs when the API stabilises" | APIs stabilise faster when you document them. The doc is the first test of the design. |
| "Comments get outdated" | Comments on *why* are stable. Comments on *what* get outdated — that's why you only write the former. |

## Red Flags

- [ ] Architectural decisions with no written rationale
- [ ] Public APIs with no documentation or types
- [ ] README that doesn't explain how to run the project
- [ ] Commented-out code instead of deletion
- [ ] TODO comments left for weeks
- [ ] `AGENTS.md` or `CONTEXT.md` out of date with the current architecture

## Verification

After documenting:

- [ ] ADRs exist for all significant architectural decisions made in this work
- [ ] README covers quick start, command table, and architecture overview
- [ ] API functions have parameter and return type documentation
- [ ] Known gotchas are documented inline where they matter
- [ ] No commented-out code remains
- [ ] Agent context files are current and accurate

## Constraints

### MUST DO
- Write ADRs for hard-to-reverse, surprising, or genuine trade-off decisions.
- Store ADRs in `docs/adrs/` with sequential numbering.
- Never delete old ADRs — they capture historical context. Supersede them with a new ADR.
- Comment only the *why*, never the *what*.

### MUST NOT DO
- Document obvious code.
- Add comments that restate what the code already says.
- Leave TODO comments for things that should just be done now.
- Leave commented-out code — git has history.
