# PRD Template

## Problem Statement & Solution

Each from the user's perspective.

## User Stories

Long numbered list: "As an `<actor>`, I want `<feature>`, so that `<benefit>`."

## Implementation Decisions

Describe decisions, not implementation. No file paths or code snippets:

- **Modules** — what will be built or modified
- **Interfaces** — public interfaces at the identified seams
- **Architectural decisions** — deep modules, seams, adapters
- **Schema changes** — data model additions or modifications
- **API contracts** — endpoints, request/response, error modes

**Exception:** if a prototype snippet encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline it within the relevant decision and note it came from a prototype. Trim to decision-rich parts only.

## Testing Decisions

- What makes a good test (external behaviour only)
- Which modules will be tested
- Prior art in the codebase

## Out of Scope

Explicitly excluded items.

## Further Notes

Additional context for the implementer.
