---
name: dev-php
description: Use when writing or fixing PHP code, implementing classes, traits, or interfaces, applying PSR standards, or working with PHP 8.3+ patterns like readonly properties, enums, named arguments, match expressions, and union types.
category: standards
---

## Core Workflow

1. **Identify the type** — DTO, Value Object, Exception, Service, or general class
2. **Apply the pattern** — use the reference guides below for the correct structure
3. **Type everything** — parameters, properties, return types including `void` and nullable
4. **Validate** — run static analysis and tests before finishing

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| DTOs | `references/dto.md` | Transferring data between layers, API responses, command objects |
| Value Objects | `references/value-objects.md` | Domain primitives, typed wrappers around scalars |
| Exceptions | `references/exceptions.md` | Custom exception classes, error context |

## Constraints

### MUST DO

- PHP 8.3+ only — use readonly classes, enums, named arguments, match expressions, union types
- `declare(strict_types=1)` in every file
- Type hint all parameters, properties, and return types including `void` and nullable
- `final readonly` for DTOs and Value Objects
- PSR-12 coding standards
- Write Pest tests for all new functionality

### MUST NOT DO

- Suppress errors with `@`
- Use global variables
- Use deprecated syntax or dynamic variable names (`$$var`)
