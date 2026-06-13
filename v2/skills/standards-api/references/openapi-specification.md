# OpenAPI Specification

## Conventions

- Use OpenAPI 3.1.0 (JSON Schema 2020-12 compatibility).
- One spec file per API version: `openapi-v1.yaml`, `openapi-v2.yaml`.
- Every endpoint must have `summary`, `operationId`, and at least one success + one error response.
- All security schemes defined at the top level; endpoints reference them by name.

## Validation

- Run `npx @redocly/cli lint openapi.yaml` before every deployment.
- Spec must be checked into version control alongside the code it describes.
- CI must reject PRs that introduce endpoints without matching spec entries.

## Response shapes

- `200` — success with `data` envelope
- `201` — created with `data` envelope
- `204` — no content (delete, update returning nothing)
- `400` — validation error with `error.details`
- `401` — unauthenticated
- `403` — unauthorized
- `404` — not found
- `422` — validation failure with `error.details`
- `500` — server error (no details exposed)
