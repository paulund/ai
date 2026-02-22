# Laravel-Specific Security Checks

Additional checks for Laravel applications beyond the general checklist.

---

## Eloquent & Database

- Avoid `$model->forceFill()` with user input — bypasses `$fillable` guard
- Confirm `$guarded = []` is never used on models with privileged fields
- Check that `HasResourceId` (or equivalent UUID concern) is applied to all public-facing models
- Verify route model binding uses UUID/resource_id, not integer id

## Form Requests

- `authorize()` should perform real authorization checks, not just return `true`
- Validation rules must cover all fields used in the action/controller
- `'url:https'` rule for outbound URL fields (enforces HTTPS-only)
- Use `'bail'` on expensive validation rules to avoid unnecessary work

## Middleware Stack

Verify the following middleware are correctly placed:

| Middleware | Applied To |
|-----------|-----------|
| `auth` or `auth:sanctum` | All authenticated routes |
| `verified` | Routes requiring email verification |
| `throttle:60,1` | API routes |
| `throttle:10,1` | Auth routes (register, password reset) |
| CSRF (`web` group) | All state-changing web routes |
| `VerifySchedulerToken` | Scheduler endpoint |

## Inertia.js

- `HandleInertiaRequests::share()` must NOT pass the raw `$request->user()` model — create an allowlist
- Sensitive fields (Stripe IDs, OAuth IDs, internal flags) must be in `$hidden` or excluded from the shared prop
- Check that `auth.user.is_admin` is not exposed to regular users via shared props

## Broadcasting / Reverb

- `routes/channels.php`: all private/presence channels must verify ownership
- Channel names must use UUIDs, not sequential integer IDs
- `allowed_origins` in `config/reverb.php` must not be `['*']` in production

## Cashier / Stripe

- Stripe webhook route must be in the CSRF exemption list
- Stripe webhook must use `VerifyWebhookSignature` middleware (built into Cashier)
- Webhook secret (`STRIPE_WEBHOOK_SECRET`) must be set in production
- Subscription status checks (`isPro()`, `isBusiness()`) must be enforced consistently:
  - Rate limiting
  - URL creation limits
  - Feature gating in controllers and policies
  - API resource serialization (don't return Pro-only data to free users)

## Laravel Pennant (Feature Flags)

- Feature checks must gate the full flow, not just the UI
- Server-side feature checks in controllers/actions, not just frontend conditionals

## Filament Admin

- `canAccessPanel()` must check `is_admin === true`
- Admin panel routes must be excluded from rate-limit policies that could lock out admins
- Filament excluded from test coverage is acceptable, but admin actions should be manually audited

## Scheduler / Queue

- Scheduler endpoint must require authentication even in non-local environments
- Jobs must not accept user-controlled data that affects which model methods are called
- Queue connection must use database or Redis — not sync in production
- Sensitive data in job payloads is serialised; ensure queued models don't expose secrets

## Social Authentication

- Verify OAuth `state` parameter is validated in callbacks
- Do not auto-link social accounts to existing email-based accounts without verification
- Email from OAuth provider should be checked as verified before linking
- Notify users via email when a new OAuth provider is linked to their account

## File Export

- `Content-Disposition` filenames must be sanitised (alphanumeric + `_-` only)
- Export routes must check ownership of the exported resource
- Exports should be streamed, not buffered in memory for large datasets

## Configuration Checks

```bash
# These must be set correctly in production:
APP_DEBUG=false
APP_ENV=production
SESSION_SECURE_COOKIE=true
SESSION_ENCRYPT=true
SCHEDULER_SECRET=<strong-random-token>
STRIPE_WEBHOOK_SECRET=<from-stripe-dashboard>
```
