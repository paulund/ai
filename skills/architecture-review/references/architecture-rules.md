# Architecture Rules

A boundary is justified when it increases cohesion within and decreases coupling
between modules. Boundaries that do neither are indirection.

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
