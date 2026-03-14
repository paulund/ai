# Idempotency

PUT/DELETE and identical PATCH requests should have the same effect when repeated.

Use Idempotency-Key header for POST operations that must not duplicate (e.g. payments).
