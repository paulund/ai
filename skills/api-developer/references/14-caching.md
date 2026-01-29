# 14. Caching

- Reference for: API Developer Skill
- Load when: When implementing HTTP caching for GET endpoints.

Use Cache-Control / ETag on cacheable GET responses.

Clients send If-None-Match; respond 304 when unchanged.

Speeds clients & reduces server load.

### Cache Headers

```http
Cache-Control: public, max-age=3600
Cache-Control: private, no-cache
Cache-Control: no-store
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Last-Modified: Wed, 15 Jan 2024 10:30:00 GMT
```

### Conditional Requests

```http
GET /users/123
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"

Response: 304 Not Modified
```

```http
PUT /users/123
If-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Content-Type: application/json

{
  "name": "Updated Name"
}

Response: 412 Precondition Failed (if ETag doesn't match)
```
