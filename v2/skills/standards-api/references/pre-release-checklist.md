# Pre-Release Checklist

Before releasing a new API or major endpoint:

## Quality

- [ ] OpenAPI spec is up to date and validated
- [ ] All endpoints have request validation
- [ ] Auth guards in place on every protected endpoint
- [ ] Rate limiting configured
- [ ] CORS configured for intended origins

## Testing

- [ ] Every endpoint has success + error path test coverage
- [ ] Contract tests pass (spec matches implementation)
- [ ] Load test passes for projected traffic (if endpoint is high-traffic)

## Docs

- [ ] OpenAPI spec includes request/response examples
- [ ] Deprecation headers on old endpoints (if versioning)
- [ ] Changelog entry added
