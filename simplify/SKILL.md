---
name: simplify
description: Identify and remove unnecessary complexity in the codebase. Use when code feels over-engineered, before refactoring, or as a cleanup step after implementation. Trigger phrases - "/simplify", "simplify this", "clean up".
category: workflow
---

## Core Workflow

1. **Identify complexity** — redundant abstractions, dead code, over-engineered solutions
2. **Measure impact** — does the complexity add value or just indirection?
3. **Propose simplifications** — concrete refactors with before/after
4. **Verify behavior preservation** — ensure functionality stays the same
5. **Apply changes** — refactor, remove, consolidate

## Simplification Targets

- [ ] Unused imports, variables, or functions
- [ ] Redundant abstractions (wrapper around a wrapper)
- [ ] Over-engineered patterns (factory for a single class)
- [ ] Deep nesting that can be flattened
- [ ] Duplicate logic that can be extracted
- [ ] Unnecessary indirection (interface with one implementation)
- [ ] Comments that explain what the code does (not why)
- [ ] Magic numbers or strings without constants

## Output Format

1. **Complexity found** — location and description
2. **Proposed simplification** — specific code changes
3. **Risk assessment** — what could break and how to test
4. **Application** — execute the refactor or create an issue
