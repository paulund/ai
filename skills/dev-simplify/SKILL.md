---
name: dev-simplify
description: Use when code feels over-engineered, before refactoring, or as a cleanup step after implementation. Identifies and removes unnecessary complexity in the codebase.
---

## Core Workflow

1. **Identify complexity** — redundant abstractions, dead code, over-engineered solutions, shallow modules that fail the deletion test.
2. **Measure impact** — apply the deletion test: would removing this concentrate complexity (keep) or scatter it (remove)? Only remove what scatters.
3. **Propose simplification** — concrete refactors with before/after and risk assessment.
4. **Verify behaviour preservation** — write tests against the public interface first if none exist (RED→GREEN). Stop and file a `hitl` issue if the gap is too large.
5. **Apply changes** — refactor, remove, consolidate.
6. **Commit** — `git add -A && git commit -m "simplify: <summary>" && git push`.

### Simplification Targets

- [ ] Unused imports, variables, functions
- [ ] Redundant abstractions (wrapper around a wrapper)
- [ ] Over-engineered patterns (factory for a single class)
- [ ] Deep nesting that can be flattened
- [ ] Duplicate logic that can be extracted
- [ ] Pass-through wrappers failing all "keep" checks
- [ ] Comments explaining *what*, not *why*
- [ ] Magic numbers/strings without constants

Code-level cleanup only. Does not propose new seams, consolidate unrelated files, or touch anything passing a "keep" rule.

## Reference Guide

| File | When to load |
|------|-------------|
| `references/architecture-rules.md` | When evaluating whether a boundary or abstraction is justified |
| `references/language.md` | When writing simplification proposals or documentation |

## Constraints

- MUST verify behaviour is preserved with tests before and after changes
- MUST NOT abstract until the pattern appears 3 times (AHA principle)
- MUST NOT propose new seams or consolidate unrelated files
- MUST NOT remove anything documented as a deliberate boundary
- MUST NOT assume a boundary is removable without checking all "keep" rules
