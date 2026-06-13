---
name: code-simplification
description: Use when code feels over-engineered, before refactoring, or as a cleanup step after implementation. Use when asked to simplify, reduce complexity, or make code clearer. Do NOT use for performance optimization, feature work, or architecture redesign.
---

## Core Workflow

1. **Identify complexity** — scan for redundant abstractions, dead code, over-engineered patterns, shallow modules, deep nesting, magic numbers, comments explaining *what* not *why*.
2. **Apply the deletion test** — would removing this concentrate complexity (keep) or scatter it (remove)? Only remove what scatters.
3. **Check Chesterton's Fence** — was this deliberately placed? Documented as a boundary in an ADR or CONTEXT.md? If yes, keep it. If no, challenge it.
4. **Propose change** — concrete before/after with risk assessment. Present to user.
5. **Verify behaviour preservation** — write tests against the public interface first if none exist (RED→GREEN). If the test gap is too large, file a `hitl` issue.
6. **Apply change** — refactor, remove, consolidate.
7. **Commit** — `git add -A && git commit -m "simplify: <summary>" && git push`.

### Simplification Targets

- [ ] Unused imports, variables, functions
- [ ] Redundant abstractions (wrapper around a wrapper)
- [ ] Over-engineered patterns (factory for a single class)
- [ ] Deep nesting that can be flattened
- [ ] Duplicate logic that can be extracted
- [ ] Pass-through wrappers failing all "keep" rules
- [ ] Comments explaining *what*, not *why*
- [ ] Magic numbers/strings without named constants
- [ ] Dead code paths that can never be reached

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This abstraction will be useful later" | Speculative generality is bloat. Wait for the third occurrence (AHA). |
| "The code works, don't touch it" | Working code can still be unnecessarily complex. Simplicity reduces future bug cost. |
| "It's just one small wrapper" | Every wrapper is indirection. A pass-through wrapper that concentrates no logic is noise. |
| "But I already wrote it" | Sunk cost. If it fails the deletion test, remove it. |
| "Someone might need this interface someday" | They'll create it when they need it. Don't build bridges to nowhere. |

## Red Flags

- [ ] You're removing something documented as a deliberate boundary
- [ ] You can't write a test to verify behaviour is preserved
- [ ] The simplification would change the public API
- [ ] You're proposing new abstractions instead of removing them
- [ ] You're consolidating unrelated files into one module

## Verification

- [ ] Behaviour preserved — tests pass before and after
- [ ] Deletion test applied — complexity scatters on delete? Keep it.
- [ ] Chesterton's Fence checked — deliberate boundaries preserved
- [ ] AHA rule respected — pattern must appear 3 times before abstracting
- [ ] All changes committed and pushed

## Constraints

### MUST DO
- Verify behaviour is preserved with tests before and after changes.
- Apply the deletion test to every candidate for removal.
- Check Chesterton's Fence — respect documented boundaries.
- Present changes with before/after and risk assessment.

### MUST NOT DO
- Abstract until the pattern appears 3 times (AHA principle).
- Remove anything documented as a deliberate boundary in ADRs or CONTEXT.md.
- Propose new seams, interfaces, or consolidate unrelated files.
- Change public API signatures.
- Remove something that passes any "keep" rule.

## Reference Guide

| File | When to load |
|------|-------------|
| `references/architecture-rules.md` | When evaluating whether a boundary or abstraction is justified |
| `references/language.md` | When writing simplification proposals or documentation |
