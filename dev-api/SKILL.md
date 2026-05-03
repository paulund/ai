---
name: dev-api
description: Use when designing, building, documenting, or maintaining RESTful APIs in this project. Enforces the project's style guide, response envelopes, pagination, field naming, and release readiness.
category: standards
---

## Core Workflow

1. **Analyze requirements** — define resources, operations, and auth requirements.
2. **Design endpoints** — plural nouns, hyphens, no verbs, no camelCase in URLs. Decide on versioning if breaking changes are possible.
3. **Implement endpoints** — enforce the response shape, error envelope, pagination, field naming, and datetime rules from Constraints below.
4. **Secure** — enforce HTTPS, validate and sanitize all inputs, and implement authentication per project requirements.
5. **Document** — update the OpenAPI spec before any code is deployed; include request, success, and error examples.
6. **Test** — cover success paths, error paths, edge cases, and security scenarios.
7. **Release** — run the Pre-Release Checklist.

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
- Return meaningful HTTP status codes and never expose stack traces, internal IDs, or secrets in responses.
- Include a request/correlation ID header for tracing.
- Version breaking changes with a URL prefix (`/v1/`, `/v2/`). Confirm with the user before introducing breaking changes.
- Update the OpenAPI spec before deploying any endpoint change. Validate the spec in CI to catch drift early.
- Use HTTPS everywhere and validate/sanitize all inputs; reject early.

### MUST NOT DO

- Mix pagination styles, error envelope shapes, or field casing across endpoints in the same API.
- Use verb-based endpoint names (e.g., `/getUsers`) or camelCase in URLs.
- Introduce breaking changes without versioning and user confirmation.
- Ship an endpoint without updating the OpenAPI spec.
- Neglect testing — every endpoint needs success and failure path coverage.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| OpenAPI Specification | [references/openapi-specification.md](references/openapi-specification.md) | Writing, updating, or validating an OpenAPI spec |
| Pre-Release Checklist | [references/pre-release-checklist.md](references/pre-release-checklist.md) | Before releasing a new API or major endpoint |
