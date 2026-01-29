# 4. Status Codes

- Reference for: API Developer Skill
- Load when: When choosing HTTP status codes for API responses.

### Success Codes (2xx)

- **200 OK** - Request succeeded (GET, PUT, PATCH)
- **201 Created** - Resource created (POST), include Location header
- **202 Accepted** - Request accepted for async processing
- **204 No Content** - Success with no response body (DELETE)

### Redirection (3xx)

- **301 Moved Permanently** - Resource permanently moved
- **302 Found** - Temporary redirect
- **304 Not Modified** - Cached version is still valid

### Client Errors (4xx)

- **400 Bad Request** - Invalid request syntax or validation error
- **401 Unauthorized** - Authentication required or failed
- **403 Forbidden** - Authenticated but not authorized
- **404 Not Found** - Resource doesn't exist
- **405 Method Not Allowed** - HTTP method not supported for resource
- **409 Conflict** - Request conflicts with current state (e.g., duplicate)
- **422 Unprocessable Entity** - Valid syntax but semantic errors
- **429 Too Many Requests** - Rate limit exceeded

### Server Errors (5xx)

- **500 Internal Server Error** - Unexpected server error
- **502 Bad Gateway** - Invalid response from upstream server
- **503 Service Unavailable** - Server temporarily unavailable
- **504 Gateway Timeout** - Upstream server timeout

Stay consistent; avoid inventing non-standard codes.
