# ADR template (Nygard format)

Use this template when the skill drafts a new ADR. Numbering is sequential, never reuse a number. Filename: `NNN-kebab-case-title.md`.

```markdown
# NNN. Title in Imperative Mood

**Status:** Proposed | Accepted | Deprecated | Superseded by NNN

**Date:** YYYY-MM-DD

## Context

What is the issue we're seeing that's motivating this decision? What forces are at play (technical, business, political)? What constraints exist? 2–4 paragraphs max.

## Decision

What did we decide? One sentence if possible. Use the active voice ("We will …" / "We chose X over Y").

## Consequences

What becomes easier? What becomes harder? What did we trade away? What new risks did we accept?

## Alternatives considered

- **Alternative A** — short description. Why rejected.
- **Alternative B** — short description. Why rejected.

## References

- Link to relevant docs, RFCs, prior art.
```

## Rules

- ADRs are **immutable history**. Never edit a past ADR. If the decision changed, write a new one that supersedes the old (set `Status: Superseded by NNN` on the old).
- Keep ADRs short. If an ADR exceeds 100 lines, the decision is probably too broad — split it.
- Don't write ADRs for reversible decisions. The "irreversibility test": would future devs need to read this to understand *why* the code looks the way it does? If no, skip it.
- Don't write ADRs that just restate docs. Reference docs instead.

## Examples already in this repo

- `docs/adr/001-data-fetching-architecture.md` — canonical Nygard format
- `docs/adr/003-design-patterns.md` — long-form ADR with code samples, still well-structured
- `docs/adr/006-drizzle-migration.md` — recent, good model for new ADRs