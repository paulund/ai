# Security Audit Checklist

Use this checklist to ensure all security domains are covered in every audit. Mark each domain as clean or list findings.

---

## 1. Authentication & Authorization

- [ ] Auth middleware applied to all routes that require it
- [ ] Policy/Gate checks on all resource operations (read, write, delete)
- [ ] No IDOR — resource ownership verified before access
- [ ] OAuth/social login: email linking does not allow account takeover
- [ ] Password reset tokens: single-use, time-limited, not guessable
- [ ] Session fixation: session regenerated on login
- [ ] Remember-me tokens: securely stored and rotated

## 2. Input Validation & Mass Assignment

- [ ] All user input validated via Form Requests before use
- [ ] No raw `$request->all()` passed to `create()`/`fill()` on models
- [ ] `$fillable` does not include privileged fields (`is_admin`, `role`, etc.)
- [ ] File uploads: type/size validated, stored outside webroot, renamed

## 3. SQL Injection

- [ ] No `DB::raw()`, `selectRaw()`, `whereRaw()`, `orderByRaw()` with unbound user input
- [ ] All query values use Eloquent bindings or `?` placeholders
- [ ] No string concatenation in query construction

## 4. XSS (Cross-Site Scripting)

- [ ] No `dangerouslySetInnerHTML` with user-controlled data in React
- [ ] No `{!! !!}` (unescaped) Blade output with user data
- [ ] No `v-html` with user data (Vue, if applicable)
- [ ] `innerHTML` / `document.write` not used with user data in JS

## 5. CSRF

- [ ] All state-changing web routes protected by CSRF middleware
- [ ] CSRF exemptions limited to: public webhooks, signed URLs, API endpoints with token auth
- [ ] Stripe/payment webhooks verified by signature, not CSRF token

## 6. SSRF (Server-Side Request Forgery)

- [ ] All user-supplied URLs validated before use in HTTP requests
- [ ] Private/internal IP ranges blocked: 10.x, 172.16-31.x, 192.168.x, 127.x, 169.254.x
- [ ] DNS rebinding mitigated: IP checked at request time, not just at validation time
- [ ] IPv6 addresses also covered (::1, fc00::/7, fe80::/10)
- [ ] Cloud metadata endpoints blocked (169.254.169.254, fd00:ec2::254)

## 7. Secrets & Credentials

- [ ] No hard-coded API keys, passwords, or tokens in source files
- [ ] All secrets loaded via `env()` / `config()`
- [ ] `.env` file is in `.gitignore` and not committed
- [ ] `APP_DEBUG=false` in production
- [ ] Different credentials per environment (dev / staging / prod)

## 8. Rate Limiting & Brute Force

- [ ] Login endpoint: rate-limited per IP and/or email
- [ ] Registration endpoint: rate-limited per IP
- [ ] Password reset: rate-limited
- [ ] API endpoints: throttle middleware applied
- [ ] Webhook capture: rate-limited appropriately

## 9. Security Headers

- [ ] `Content-Security-Policy`
- [ ] `X-Frame-Options: SAMEORIGIN` or `frame-ancestors 'self'`
- [ ] `X-Content-Type-Options: nosniff`
- [ ] `Referrer-Policy: strict-origin-when-cross-origin`
- [ ] `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- [ ] `Permissions-Policy`

## 10. Data Exposure

- [ ] No integer primary keys in public URLs or API responses
- [ ] UUIDs / resource_ids used for all public-facing identifiers
- [ ] Sensitive user fields not serialised to frontend (payment data, OAuth IDs, admin flags)
- [ ] API resources use allowlists (explicit `toArray()`), not `$model->toArray()`
- [ ] No stack traces or debug info exposed in production

## 11. Session & Cookies

- [ ] `SESSION_SECURE_COOKIE=true` in production
- [ ] `SESSION_SAME_SITE=lax` or `strict`
- [ ] `SESSION_ENCRYPT=true` recommended
- [ ] Cookies use `HttpOnly` flag (Laravel default)
- [ ] Session driver appropriate for scale (database/redis, not file in multi-instance)

## 12. WebSocket / Real-time Security

- [ ] Channel authorization middleware verifies ownership
- [ ] `allowed_origins` restricted to known domains (not `['*']`)
- [ ] Private channel names use UUIDs, not sequential IDs

## 13. Third-party Integrations

- [ ] Payment webhooks: signature verified (Stripe `WEBHOOK_SECRET`)
- [ ] OAuth callbacks: `state` parameter validated (CSRF protection)
- [ ] Outbound HTTP: redirects limited/disabled to prevent redirect-based SSRF
- [ ] External API keys scoped to minimum required permissions

## 14. Admin / Privileged Access

- [ ] Admin panel behind authentication and authorization check
- [ ] Admin routes not discoverable by non-admin users
- [ ] Privileged fields not mass-assignable
- [ ] Audit log for admin actions (if applicable)

## 15. Infrastructure & Configuration

- [ ] Scheduler endpoint authenticated (not publicly triggerable)
- [ ] Queue workers: jobs cannot be injected with malicious payloads
- [ ] File storage: uploaded files served via signed URLs, not public paths
- [ ] CORS: `allowed_origins` restricted, `supports_credentials` only where needed
- [ ] Docker/container: runs as non-root user, no unnecessary capabilities
