---
description: Run TDD workflow — write tests to prove behaviour. Use "/test prove-it" for bugs.
---

Invoke the `test-driven-development` skill.

## Modes

- **`/test`** — standard TDD cycle. For each behaviour or acceptance criterion: write a failing test (RED), implement the minimum code to pass (GREEN), refactor. Run `quality-gate` after the last cycle.
- **`/test prove-it`** — bug-fix mode. Write a test that reproduces the bug, watch it fail, fix the root cause, watch it pass, add a regression test. Run the full suite.

### Test quality checks

- Tests describe behaviour, not implementation
- Tests use public interfaces only
- Tests would survive an internal refactor
- No mocks of types you don't own
