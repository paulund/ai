# Frontend Security Checks

Security checks specific to React + TypeScript + Inertia.js frontends.

---

## React / JSX

- [ ] No `dangerouslySetInnerHTML` with user-controlled data
- [ ] No `innerHTML` or `document.write` with user data in event handlers or effects
- [ ] URL values from user input are not used in `href` / `src` without validation (javascript: protocol injection)
- [ ] `<a href={userInput}>` — validate that the href begins with `https://` or `/`, not `javascript:`

## TypeScript Type Safety

- [ ] `id` fields in public-facing types are `string` (UUID), never `number`
- [ ] No `any` types on data received from the server that could mask unexpected fields
- [ ] API response types match the backend API Resource serialization

## Inertia.js Props

- [ ] Sensitive auth props (`is_admin`, payment details) not used to gate UI-only — must also be gated server-side
- [ ] `usePage().props.auth.user` — verify which fields are present and that none are unexpectedly sensitive
- [ ] CSRF token is correctly sent on all Inertia form submissions (Inertia handles this automatically, but verify no custom fetch() calls bypass it)

## Custom Fetch / Axios Calls

- [ ] Any non-Inertia HTTP requests include the CSRF token in headers
- [ ] API calls do not expose bearer tokens in URL query strings (use `Authorization` header)
- [ ] No secrets (API keys, tokens) stored in `localStorage` or `sessionStorage` — use HttpOnly cookies

## Third-party Scripts

- [ ] External scripts loaded from CDNs use `integrity` (SRI hash) attributes
- [ ] Google Fonts / external stylesheets are allowlisted in CSP
- [ ] No eval()-based code or `new Function()` with dynamic strings

## Environment Variables

- [ ] `VITE_` prefixed variables do not contain secrets — they are embedded in the client bundle
- [ ] Only public, non-sensitive values are exposed as `VITE_` variables
- [ ] Secret API keys are server-side only (`config()` / `env()` in PHP)

## Wayfinder / Route Helpers

- [ ] Route helpers use `.url()` as a function call (not property access)
- [ ] Route parameters are not constructed from raw user input without encoding

## Real-time / WebSocket Client

- [ ] Echo channel subscriptions are to private channels that require server-side auth
- [ ] Channel names use UUIDs, not guessable sequential IDs
- [ ] No sensitive data broadcast on public (unauthenticated) channels
