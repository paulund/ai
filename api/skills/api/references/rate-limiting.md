# Rate Limiting & Throttling

Limit requests (e.g. 60/min per API key).
Return headers: X-Rate-Limit-Limit,X-Rate-Limit-Remaining, X-Rate-Limit-Reset.

Helps prevent abuse & accidental runaway scripts.
