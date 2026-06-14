---
description: Simplify code — remove unnecessary complexity while preserving behaviour. Clarity over cleverness.
---

Invoke the `code-simplification` skill.

1. Read AGENTS.md (or CLAUDE.md) and study project conventions before touching any code.
2. Identify the target code — recent changes unless a broader scope is specified.

Identify and remove unnecessary complexity. Run the deletion test on every candidate — would removing this concentrate complexity (keep) or scatter it (remove)?

## Targets

- Redundant abstractions (wrapper around a wrapper)
- Premature abstractions (first-use pattern extraction)
- Deep nesting that can be flattened
- Dead code, unused imports, unreachable branches
- Pass-through wrappers failing all "keep" rules
- Comments explaining *what* not *why*
- Magic numbers and strings without named constants

## Workflow

1. Scan for complexity targets.
2. Apply Chesterton's Fence — was this deliberately placed? Documented boundary?
3. Propose simplification with before/after. Get user confirmation.
4. Apply each simplification incrementally — run tests after each individual change. If tests fail, revert that specific change and reconsider before moving on.
5. Verify all tests pass and the build succeeds across the full set of changes.
6. Commit, push.
