---
description: Simplify code — remove unnecessary complexity while preserving behaviour. Clarity over cleverness.
---

Invoke the `code-simplification` skill.

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
4. Verify behaviour preservation — tests pass before and after.
5. Apply changes, commit, push.
