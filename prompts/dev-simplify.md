---
description: Identify and remove unnecessary complexity in the codebase. Use when code feels over-engineered, before refactoring, or as a cleanup step after implementation.
---

## Core Workflow

0. **Load references** — read the Architecture Rules and Language sections below.
1. **Identify complexity** — redundant abstractions, dead code, over-engineered solutions, shallow modules that fail the deletion test.
2. **Measure impact** — apply the deletion test: would removing this concentrate complexity (keep) or scatter it (remove)? Only remove what scatters.
3. **Propose simplification** — concrete refactors with before/after and risk assessment.
4. **Verify behaviour preservation** — write tests against the public interface first if none exist (RED→GREEN). Stop and file a `hitl` issue if the gap is too large.
5. **Apply changes** — refactor, remove, consolidate.
6. **Commit** — `git add -A && git commit -m "simplify: <summary>" && git push`.

## Simplification Targets

- [ ] Unused imports, variables, functions
- [ ] Redundant abstractions (wrapper around a wrapper)
- [ ] Over-engineered patterns (factory for a single class)
- [ ] Deep nesting that can be flattened
- [ ] Duplicate logic that can be extracted
- [ ] Pass-through wrappers failing all "keep" checks (see Architecture Rules)
- [ ] Comments explaining *what*, not *why*
- [ ] Magic numbers/strings without constants

Code-level cleanup only. Does not propose new seams, consolidate unrelated files, or touch anything passing a "keep" rule below.

## Reference: Architecture Rules

A boundary is justified when it increases cohesion within and decreases coupling between modules. Boundaries that do neither are indirection.

**Keep a boundary** if any of:
1. Two or more adapters exist (or concretely planned)
2. Hides a design decision the caller shouldn't know
3. Complexity scatters across callers on delete (deletion test)
4. Documented as deliberate boundary in ADR or CONTEXT.md

**Remove** only when **all** of:
1. Single adapter, no concrete plan for a second
2. No design decision hidden (transparent pass-through)
3. Complexity vanishes on delete
4. Not documented as deliberate boundary

**Rules of thumb:**
- AHA: don't abstract until the pattern appears 3 times. Duplicate first, extract later.
- Default to fewer modules. Tie-break in order: ADR → proven utility (2+ adapters) → information hiding → deletion test.

## Reference: Language

Use these terms exactly — consistent language is the point.

- **Module** — anything with an interface + implementation (function, class, package, slice).
- **Interface** — everything a caller must know: types, invariants, error modes, ordering, config.
- **Depth** — behaviour behind a small interface. Deep = high leverage. Shallow = interface nearly as complex as implementation.
- **Seam** — where behaviour can be altered without editing in place (not "boundary").
- **Adapter** — concrete thing satisfying an interface at a seam.
- **Deletion test** — delete the module. Complexity vanishes? Pass-through. Reappears across callers? Earning its keep.
- One adapter = hypothetical seam. Two adapters = real seam.
