---
name: test-driven-development
description: Use when implementing any logic, fixing any bug, or changing any behaviour. Use when you need to prove code works, when a bug report arrives, or when modifying existing functionality. Do NOT use for config changes, data migrations, or dependency updates.
---

## Core Workflow

### RED → GREEN → REFACTOR

1. **RED** — write one test that captures the desired behaviour. Run it. Watch it fail for the right reason (wrong test = test that passes without the feature).
2. **GREEN** — write the minimum code to pass. No extras, no anticipating future tests.
3. **REFACTOR** — clean up. Remove duplication, improve naming, flatten. Never refactor while RED.

### Test Pyramid (80/15/5)

- **80% unit tests** — fast, isolated, testing one module through its public interface
- **15% integration tests** — testing module interactions, database, external services
- **5% end-to-end tests** — critical user journeys through the full stack

### Prove-It Pattern (for bugs)

1. Write a test that reproduces the bug (and fails).
2. Fix the code so the test passes.
3. Add a regression test so the bug can't recur.
4. Run the full suite to verify nothing else broke.

### Test Quality Checklist

- [ ] Test describes behaviour, not implementation
- [ ] Test uses public interface only
- [ ] Test would survive an internal refactor
- [ ] Test name describes what's being proven, not what's being called
- [ ] No mocks of types you don't own
- [ ] CI would catch a regression in this behaviour

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This is too simple to need a test" | Components fail in simple ways too. If it has a bug cost, it needs a test. |
| "I'll write the tests after the code works" | Tests written after code often test the implementation, not the behaviour. They pass because they mirror the code. |
| "I'm just refactoring, tests still pass" | If tests still pass without changes, they're testing implementation, not behaviour. Good tests break when you refactor internals. |
| "I don't have time to write tests" | Untested code takes longer to debug, and bugs found in production cost 10× more. |
| "The test coverage is already good enough" | Coverage numbers measure quantity, not quality. A 100% coverage suite can miss every bug if tests don't assert the right things. |

## Red Flags

- [ ] Tests are passing before the feature is implemented
- [ ] Tests are testing internal functions or private methods
- [ ] Tests are named after the function they call, not the behaviour they prove
- [ ] You're using `any` or type casts to make the test compile
- [ ] The test setup is more complex than the test body
- [ ] Tests share mutable state or depend on test ordering

## Verification

- [ ] Every acceptance criterion has a passing test
- [ ] Test suite passes with no skipped or ignored tests
- [ ] Tests are in the correct pyramid tier (80/15/5)
- [ ] Bug fixes include a regression test
- [ ] Tests run in CI (or ran locally with the same config)
- [ ] No test uses implementation internals (spying on private methods, mocking internals)

## Constraints

### MUST DO
- Write the test first (RED) — see it fail before making it pass.
- Test through public interfaces only — tests that know internals break during refactors.
- Add regression tests for every bug fix.
- Name tests by the behaviour they prove, not the function they call.
- Run the full suite after the last GREEN — don't skip.

### MUST NOT DO
- Write tests after implementation — you'll test implementation, not behaviour.
- Mock types you don't own — wrap them in your own interface instead.
- Test implementation details (private methods, internal state).
- Skip tests to "save time" — the gate exists for a reason.
- Write all tests first (horizontal testing) — test one behaviour at a time.
