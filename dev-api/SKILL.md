---
name: dev-api
description: Use when designing, building, documenting, or maintaining RESTful APIs — including endpoint design, OpenAPI specifications, security, and release readiness.
---

## Core Workflow

1. **Analyze requirements** — understand business needs and define required endpoints.
2. **Design endpoints** — choose resources, HTTP methods, URL hierarchy, and versioning strategy.
3. **Implement endpoints** — build with proper status codes, error handling, pagination, and caching.
4. **Secure the API** — enforce HTTPS, authentication, authorization, rate limiting, and input validation.
5. **Write OpenAPI spec** — draft the specification in YAML/JSON covering all endpoints, parameters, and response schemas with examples.
6. **Validate spec** — review for accuracy, completeness, and OAS compliance; use tooling to lint.
7. **Test** — cover success paths, error paths, edge cases, and security scenarios.
8. **Release** — run pre-release checklist, publish documentation, deploy.
9. **Maintain** — monitor usage and performance, version breaking changes, deprecate responsibly, keep spec in sync.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| HTTP Methods | [references/http-methods.md](references/http-methods.md) | Deciding on appropriate HTTP methods for endpoints |
| Resource Naming | [references/resource-naming.md](references/resource-naming.md) | Naming endpoints and structuring URL hierarchies |
| Versioning | [references/versioning.md](references/versioning.md) | Planning breaking changes or deprecating API versions |
| Status Codes | [references/status-codes.md](references/status-codes.md) | Choosing HTTP status codes for responses |
| Filtering & Pagination | [references/filtering-pagination.md](references/filtering-pagination.md) | Implementing list endpoints with filtering, sorting, or pagination |
| Response Shape | [references/response-shape.md](references/response-shape.md) | Structuring JSON response envelopes for data and errors |
| Including Related Data | [references/including-related-data.md](references/including-related-data.md) | Implementing optional expansion of related resources |
| Field Naming | [references/field-naming.md](references/field-naming.md) | Defining field names in request/response payloads |
| Datetime Handling | [references/datetime-handling.md](references/datetime-handling.md) | Working with dates and times in API payloads |
| Authentication & Tokens | [references/authentication-tokens.md](references/authentication-tokens.md) | Implementing authentication or token management |
| Rate Limiting | [references/rate-limiting.md](references/rate-limiting.md) | Implementing request throttling or abuse prevention |
| Security Basics | [references/security-basics.md](references/security-basics.md) | Reviewing API security or handling untrusted input |
| Validation Errors | [references/validation-errors.md](references/validation-errors.md) | Formatting validation error responses |
| Caching | [references/caching.md](references/caching.md) | Implementing HTTP caching for GET endpoints |
| Idempotency | [references/idempotency.md](references/idempotency.md) | Ensuring safe retries for mutating operations |
| Error Handling | [references/error-handling.md](references/error-handling.md) | Mapping exceptions to API error responses |
| Documentation | [references/documentation.md](references/documentation.md) | Creating or updating API documentation |
| Deprecation | [references/deprecation.md](references/deprecation.md) | Planning to retire or replace API endpoints |
| Consistency Rules | [references/consistency-rules.md](references/consistency-rules.md) | Reviewing API design for style guide compliance |
| Pre-Release Checklist | [references/pre-release-checklist.md](references/pre-release-checklist.md) | Before releasing a new API or major endpoint |
| OpenAPI Specification | [references/openapi-specification.md](references/openapi-specification.md) | Writing, updating, or validating an OpenAPI spec |

## Constraints

### MUST DO

- Follow RESTful principles for endpoint design and HTTP method usage.
- Use HTTPS and secure all endpoints against common vulnerabilities.
- Document every endpoint in an OpenAPI spec with request, success, and error examples.
- Return meaningful HTTP status codes and consistent error envelopes.
- Validate the OpenAPI spec using tooling before publishing.
- Paginate all list endpoints with sensible defaults and max limits.
- Version breaking changes and communicate deprecation timelines.

### MUST NOT DO

- Expose sensitive data (stack traces, internal IDs, secrets) in responses.
- Introduce breaking changes without versioning and user confirmation.
- Use ambiguous language or omit details in API documentation.
- Ship an endpoint without updating the OpenAPI spec.
- Neglect testing — every endpoint needs success and failure path coverage.
