---
name: standards-api
description: Use when designing, building, documenting, or maintaining RESTful APIs, defining type contracts between modules, or establishing boundaries between frontend and backend. Enforces the project's style guide, response envelopes, pagination, field naming, and release readiness.
category: standards
global: false
public: true
---

## Core Workflow

1. **Implement endpoints** — enforce the response shape, error envelope, pagination, field naming, and datetime rules from Constraints below.
2. **Document** — update the OpenAPI spec before any code is deployed; include request, success, and error examples.
3. **Test** — cover success paths, error paths, edge cases, and security scenarios.
4. **Release** — run the Pre-Release Checklist.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "We'll document the API later" | The types ARE the documentation. Define them first. |
| "We don't need pagination for now" | You will the moment someone has 100+ items. Add it from the start. |
| "PATCH is complicated, let's just use PUT" | PUT requires the full object every time. PATCH is what clients actually want. |
| "We'll version the API when we need to" | Breaking changes without versioning break consumers. Design for extension from the start. |
| "Nobody uses that undocumented behavior" | Hyrum's Law: if it's observable, somebody depends on it. Treat every public behavior as a commitment. |
| "Internal APIs don't need contracts" | Internal consumers are still consumers. Contracts prevent coupling and enable parallel work. |

## Red Flags

- [ ] Endpoints that return different shapes depending on conditions
- [ ] Inconsistent error formats across endpoints
- [ ] Validation scattered throughout internal code instead of at boundaries
- [ ] Breaking changes to existing fields (type changes, removals)
- [ ] List endpoints without pagination
- [ ] Verbs in REST URLs (`/api/createTask`, `/api/getUsers`)
- [ ] Third-party API responses used without validation or sanitization

## Verification

After designing or implementing an API:

- [ ] Every endpoint has typed input and output schemas
- [ ] Error responses follow a single consistent format
- [ ] Validation happens at system boundaries only
- [ ] List endpoints support pagination
- [ ] New fields are additive and optional (backward compatible)
- [ ] Naming follows consistent conventions across all endpoints
- [ ] OpenAPI spec is up to date and validated
- [ ] API documentation or types are committed alongside the implementation

## Constraints

### MUST DO

- Use `snake_case` for all JSON field names, query parameters, and error detail keys.
- Return datetimes as UTC ISO 8601 with trailing `Z` (`YYYY-MM-DDTHH:MM:SSZ`).
- Wrap successful responses in a consistent envelope:
  ```json
  {
    "data": [ ... ],
    "meta": { "total": 120, "page": 2 },
    "links": { "next": "...", "prev": "..." }
  }
  ```
- Wrap errors in a consistent envelope:
  ```json
  {
    "error": {
      "code": 422,
      "message": "Validation failed",
      "details": { "field_name": ["Error message"] }
    }
  }
  ```
- Paginate all list endpoints with `page` and `per_page` query params. Enforce sensible defaults and a max limit.
- Include a request/correlation ID header for tracing.
- Version breaking changes with a URL prefix (`/v1/`, `/v2/`). Confirm with the user before introducing breaking changes.
- Update the OpenAPI spec before deploying any endpoint change. Validate the spec in CI to catch drift early.

### MUST NOT DO

- Mix pagination styles, error envelope shapes, or field casing across endpoints in the same API.
- Introduce breaking changes without versioning and user confirmation.
- Ship an endpoint without updating the OpenAPI spec.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Design Principles | [references/design-principles.md](references/design-principles.md) | Designing new APIs from scratch, defining module boundaries, or creating public interfaces |
| OpenAPI Specification | [references/openapi-specification.md](references/openapi-specification.md) | Writing, updating, or validating an OpenAPI spec |
| Pre-Release Checklist | [references/pre-release-checklist.md](references/pre-release-checklist.md) | Before releasing a new API or major endpoint |