# 19. Consistency Rules

- Reference for: API Developer Skill
- Load when: When reviewing API design for style guide compliance.

- Style guide must cover: resource naming, pagination params, error envelope, auth scheme, versioning, response envelope, datetime format, and field casing.
- Field naming: pick snake_case or camelCase and apply everywhere (JSON, query params, errors).
- Datetimes: enforce UTC ISO 8601 with trailing Z.
- Pagination: standard params page & per_page with documented defaults & max.
- Error envelope: one consistent JSON shape.
- Versioning: documented deprecation path & /v2 rollout process.
- Reviews: checklist includes adherence to style guide.

Use this as a review checklist to keep the API uniform.
