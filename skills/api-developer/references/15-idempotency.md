# 15. Idempotency

- Reference for: API Developer Skill
- Load when: When ensuring safe retries for mutating operations.

PUT/DELETE and identical PATCH requests should have the same effect when repeated.

Use Idempotency-Key header for POST operations that must not duplicate (e.g. payments).
