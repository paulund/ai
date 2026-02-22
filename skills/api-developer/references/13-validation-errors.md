# 13. Validation Errors

- Reference for: API Developer Skill
- Load when: When formatting validation error responses.

Unified format:

```json
{
    "error": {
        "code": 422,
        "message": "Validation failed",
        "details": { "field": ["Issue"] }
    }
}
```

Clients can iterate details object to display form errors.
