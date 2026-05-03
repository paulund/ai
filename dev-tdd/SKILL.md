---
name: dev-tdd
description: Use when building features or fixing bugs using test-driven development, when the user mentions "red-green-refactor", wants integration-style tests, or asks for test-first development.
---

# Test-Driven Development

## Philosophy

Tests verify behavior through public interfaces, not implementation details. Good tests survive refactors because they describe _what_ the system does, not _how_.

Warning sign: tests break during refactors where behavior hasn't changed.

## Reference Guide

| File | Load when |
|------|-----------|
| [tests.md](references/tests.md) | Writing or reviewing test cases — good vs. bad patterns |
| [mocking.md](references/mocking.md) | Deciding what to mock or designing for mockability |
| [interface-design.md](references/interface-design.md) | Designing public interfaces for testability |
| [deep-modules.md](references/deep-modules.md) | Evaluating module depth and interface size |
| [refactoring.md](references/refactoring.md) | Looking for refactor opportunities after GREEN |

## Core Workflow

### 1. Plan

Before writing any code:

- [ ] Confirm which interface changes are needed
- [ ] Confirm which behaviors to test (prioritize with the user)
- [ ] Identify opportunities for deep modules (small interface, deep implementation)
- [ ] Design interfaces for testability
- [ ] List the behaviors to test — not implementation steps
- [ ] Get user approval on the plan

Ask: "What should the public interface look like? Which behaviors are most important to test?"

You can't test everything. Confirm exactly which behaviors matter most. Focus on critical paths and complex logic, not every edge case.

### 2. Tracer Bullet

Write ONE test that confirms ONE thing:

```
RED:   Write test for first behavior → test fails
GREEN: Write minimal code to pass → test passes
```

### 3. Incremental Loop

For each remaining behavior:

```
RED:   Write next test → fails
GREEN: Minimal code to pass → passes
```

Rules:
- One test at a time
- Only enough code to pass the current test
- Don't anticipate future tests
- Keep tests focused on observable behavior

### 4. Refactor

After all tests pass, check [refactoring.md](references/refactoring.md):

- [ ] Extract duplication
- [ ] Deepen modules
- [ ] Apply SOLID principles where natural
- [ ] Run tests after each refactor step

**Never refactor while RED.** Get to GREEN first.

## Constraints

**MUST DO**
- Use vertical slices: one test → one implementation → repeat. Never write all tests first then all code (horizontal slicing).
- Test only through public interfaces
- Write tests that describe WHAT, not HOW
- Confirm the plan with the user before starting

**MUST NOT DO**
- Mock internal collaborators or your own modules
- Test private methods or internal state
- Add speculative features not covered by a test
- Refactor while any test is RED
