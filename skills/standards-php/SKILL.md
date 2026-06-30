---
name: standards-php
description: Use when writing or fixing PHP code, implementing classes, traits, or interfaces, applying PSR standards, or working with PHP 8.3+ patterns like readonly properties, enums, named arguments, match expressions, and union types.
category: standards
global: false
public: true
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

- `final readonly` for DTOs and Value Objects
- Write Pest tests for all new functionality

### MUST NOT DO

- Use dynamic variable names (`$$var`)
