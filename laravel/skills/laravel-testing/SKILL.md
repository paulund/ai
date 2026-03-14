---
name: laravel-testing
description: Use when writing or modifying Pest tests for Laravel applications, including feature tests, unit tests, and factories.
---

## Core Workflow

1. **Identify scope** — determine what needs testing (feature, unit, integration)
2. **Set up factories** — create or update model factories with realistic data
3. **Write tests** — Pest feature tests for HTTP flows, unit tests for isolated logic
4. **Run suite** — `./vendor/bin/sail composer run test`
5. **Achieve coverage** — target 100% coverage on new code

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Factories | `references/factories.md` | Model factories, states, relationships |
| Unit Testing | `references/unit-testing.md` | Testing actions, services, models |
| Inertia Testing | `references/inertia-testing.md` | Testing Inertia responses, page props |

## Constraints

### MUST DO

- Use Pest (not PHPUnit class syntax)
- Use `RefreshDatabase` trait for database tests
- Test both happy path and error cases
- Assert specific validation errors, not just failure
- Use factories with explicit attributes — don't rely on defaults for tested values
- Test authorization (policy checks) in feature tests

### MUST NOT DO

- Mock Eloquent models — use real database with RefreshDatabase
- Test framework internals (e.g. that Laravel validates correctly)
- Write tests that depend on execution order
- Use `assertStatus(200)` without also asserting response content
