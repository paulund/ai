# Eloquent Models

## Performance

1. **Always eager load relationships** — avoid N+1 queries
2. **Use chunking for large datasets** — prevent memory exhaustion
3. **Index foreign keys** — speed up joins
4. **Use `select()` to limit columns** — reduce data transfer
5. **Cache expensive queries** — use Redis/Memcached
6. **Add database indexes in migrations** — for frequently queried columns
7. **Avoid model events for heavy operations** — use queues instead
8. **Use lazy collections** — for processing large datasets

## Conventions

- Models are always `final class`
- Use `$casts` for attribute casting
- Define relationships with return types
- Use scopes for reusable query constraints
- Use `resource_id` (UUID) for public-facing identifiers
