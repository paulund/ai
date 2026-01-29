# 6. Response Shape

- Reference for: API Developer Skill
- Load when: When structuring JSON response envelopes for data and errors.

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
