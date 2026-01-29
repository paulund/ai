# 9. Datetime Handling

- Reference for: API Developer Skill
- Load when: When working with dates and times in API payloads.

Use ISO 8601 UTC timestamps everywhere.

Preferred wire format:

```
YYYY-MM-DDTHH:MM:SSZ
```

## Examples

- 2025-08-16T12:34:56Z
- 2025-01-01T00:00:00Z

## Guidelines

- Always send & store in UTC (Z suffix); convert for display client-side.
- Include seconds; add milliseconds only if required (2025-08-16T12:34:56.123Z).
- Avoid PHP ISO8601 constant variant with +0000 (missing colon).
- Avoid raw +01:00 offsets in query params (encode or normalise); normalise to UTC internally.

## Benefits

- Consistent ordering & comparisons.
- Simplifies storage & reduces timezone bugs.
