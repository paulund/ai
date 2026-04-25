# Validation Errors

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
