# Pre-Release Checklist

- [ ] OpenAPI spec complete and validated in CI
- [ ] All endpoints use `snake_case` field naming consistently
- [ ] Datetimes returned as UTC ISO 8601 with trailing `Z`
- [ ] Response envelope (`data`/`meta`/`links`) on list endpoints
- [ ] Error envelope (`error.code`/`error.message`/`error.details`) everywhere
- [ ] Pagination with `page` and `per_page` on list endpoints
- [ ] Rate limiting active
- [ ] HTTPS enforced and security headers present
- [ ] Tests covering success paths, failure paths, and edge cases
