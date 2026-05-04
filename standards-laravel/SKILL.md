---
name: standards-laravel
description: Use when building or modifying Laravel applications, including routes, controllers, models, migrations, jobs, actions, and API resources.
category: standards
---

## Core Workflow

1. **Analyse requirements** — understand scope, identify models, routes, and business logic
2. **Design** — plan migrations, models, actions, controllers, form requests, resources
3. **Implement** — write code following Laravel conventions and the reference guides below
4. **Test** — write Pest feature and unit tests for all new functionality
5. **Verify** — run `./vendor/bin/sail composer run test` (Pint + PHPStan level 8 + Pest)

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Models | `references/models.md` | Eloquent models, relationships, scopes, casts |
| Routes | `references/routes.md` | Route definitions, naming, grouping, middleware |
| Controllers | `references/controllers.md` | Invokable controllers, route model binding, return types |
| Actions | `references/actions.md` | Business logic, record creation/update/delete |
| Form Requests | `references/form-requests.md` | Validation, authorization |
| API Resources | `references/api-resources.md` | Model transformation, nested resources, Inertia props |
| Services | `references/services.md` | External I/O, third-party API clients, DTOs |
| Jobs & Events | `references/jobs-events.md` | Queued jobs, event dispatching, listeners |
| Policies | `references/policies.md` | Authorization policies, gates |
| Migrations | `references/migrations.md` | Schema changes, indexes, foreign keys |
| Configuration | `references/configuration.md` | `#[Config]` attribute injection, view data passing |
| Current User | `references/current-user.md` | `#[CurrentUser]` attribute, authenticated user injection |

## Constraints

### MUST DO

- Type hint all parameters and return types
- Use Form Request classes for all validation — never `$request->validate()` in controllers
- Create an auth-only Form Request (empty `rules()`) for actions with no request body (show, edit, destroy)
- Use Action classes for all create/update/delete operations — never persist directly in controllers
- Use API Resource classes for all model transformation — never return models directly
- Use `resource_id` (UUID) as public `id` — never expose internal database IDs
- Eager load relationships to avoid N+1 queries
- Write database migrations for all schema changes
- Use `#[Config]` attribute for configuration injection
- Use `#[CurrentUser]` attribute for authenticated user injection in controller constructors
- Follow PSR-12 coding standards
- Queue long-running tasks

### MUST NOT DO

- Use raw SQL when Eloquent or query builder suffices
- Hardcode configuration values
- Use `Gate::authorize()` in controllers — use Form Request `authorize()` method
- Call `Auth::user()` inside controller methods — use `#[CurrentUser]` constructor injection instead
- Skip eager loading when accessing related models
- Mix business logic into controllers
- Use inline validation in controllers
- Return models directly or manually map in controllers
