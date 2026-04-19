# Response Shape

Wrap data in an envelope for extensibility:

```json
{
    "data": [ ... ],
    "meta": { "total": 120, "page": 2 },
    "links": { "next": "...", "prev": "..." }
}
```

Error envelope:

```json
{
    "error": {
        "code": 404,
        "message": "Not Found"
    }
}
```
