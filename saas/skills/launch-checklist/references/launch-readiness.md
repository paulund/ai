# Launch Readiness Reference

## How to Use This Checklist

Not everything here is needed for every launch. Adjust based on:

- **B2B vs B2C** — B2B needs team management, DPAs, and SSO earlier; B2C needs social login and self-serve billing
- **Charging immediately vs freemium** — if charging, billing and legal are blockers; if free, less urgent
- **Solo vs team product** — team management is a blocker for team-based products
- **EU users** — GDPR compliance is required, not optional

Work through the tiers in order. Do not add "nice-to-have" features while blockers remain.

---

## Tier 1: Must-Have Before Launch (Blockers)

### Authentication

- [ ] Email and password signup and login
- [ ] Password reset via email
- [ ] Email verification (at minimum, send verification email — can make it optional initially)
- [ ] Rate limiting on login attempts
- [ ] Session management (sessions expire, can be revoked)

### Billing (if charging money)

- [ ] Payment provider integrated (Stripe recommended)
- [ ] Subscription creation works end-to-end
- [ ] Failed payment webhook handler in place
- [ ] Subscription cancellation flow works
- [ ] Invoice / receipt emails sent on payment

### Core email flows

- [ ] Welcome email on signup
- [ ] Password reset email
- [ ] Email verification email (if requiring verification)
- [ ] Sending domain authenticated (SPF, DKIM, DMARC)

### Legal pages

- [ ] Terms of Service published
- [ ] Privacy Policy published
- [ ] Cookie consent banner (required if serving EU users and using tracking)

### Basic infrastructure

- [ ] Error tracking configured (Sentry or equivalent)
- [ ] Application logging in place
- [ ] No secrets in source code
- [ ] HTTPS enforced everywhere
- [ ] Debug mode disabled in production
- [ ] Environment variables set correctly for production

---

## Tier 2: Should-Have for Launch (Important)

### Onboarding

- [ ] Empty state screens for new accounts (not blank pages)
- [ ] Getting-started checklist or guided first steps
- [ ] Welcome modal or onboarding flow on first login
- [ ] Onboarding email sequence (minimum 3 emails over first 14 days)

### User account settings

- [ ] User can update name and profile photo
- [ ] User can change password
- [ ] User can delete their account

### Subscription management (if charging)

- [ ] User can view current plan
- [ ] User can upgrade or downgrade
- [ ] User can cancel subscription
- [ ] User can view billing history and download invoices

### Product and infrastructure

- [ ] Branded 404 page (not a generic server error)
- [ ] Branded 500 page that does not expose stack traces
- [ ] Support email address published and monitored
- [ ] Health check endpoint at `/health` or equivalent
- [ ] Uptime monitoring configured
- [ ] Database backups configured and tested

### SEO basics

- [ ] Meta title and description on all public pages
- [ ] Sitemap submitted to Google Search Console
- [ ] Analytics configured
- [ ] Privacy-friendly analytics preferred (Plausible, Fathom) if targeting privacy-conscious users

---

## Tier 3: Post-Launch Features (Add Later)

### Team and organisation management

- [ ] Organisation creation
- [ ] Member invitation by email
- [ ] Role-based permissions (owner, admin, member)
- [ ] Member removal
- [ ] Seat-based billing (if applicable)

### Advanced authentication

- [ ] OAuth providers (Google, GitHub)
- [ ] Two-factor authentication (TOTP)
- [ ] SSO/SAML (enterprise tier)

### API and developer features

- [ ] API key management
- [ ] API rate limiting
- [ ] Outbound webhook subscriptions
- [ ] Webhook delivery logs
- [ ] Public API documentation

### MCP (Model Context Protocol) server

An MCP server lets users connect your product to AI assistants (Claude, Cursor, etc.) so they can query and interact with your product's data using natural language. It is a developer-facing feature that signals AI-native product thinking.

- [ ] MCP server implemented exposing core resources and tools
- [ ] Resources defined — read-only data the AI can access (e.g., list records, get record by ID)
- [ ] Tools defined — actions the AI can take (e.g., create, update, delete where appropriate)
- [ ] Authentication via existing API keys (reuse the API key system — do not invent a separate auth mechanism)
- [ ] MCP server configuration published (`mcp.json` or equivalent, installable with one command)
- [ ] Documentation: how to add the MCP server to Claude Desktop, Cursor, or other MCP clients
- [ ] Rate limiting applied (reuse the API tier rate limits)
- [ ] Scoped permissions: tools only available when the API key has the appropriate scope

**When to add this:**
Add MCP support when you have a working API. It is a thin layer over existing endpoints, not a rebuild. If your product manages data that would be useful to reference inside an AI conversation, MCP makes your product discoverable to AI-native users early.

### Audit and compliance

- [ ] Audit log of significant events
- [ ] Data export (GDPR right to access)
- [ ] Data deletion workflow (GDPR right to erasure)
- [ ] DPAs available for B2B customers

### Admin and operations

- [ ] Internal admin panel (user management, subscription overrides)
- [ ] Feature flag management
- [ ] In-app notification centre
- [ ] Public status page

### Growth features

- [ ] Changelog / release notes page
- [ ] Referral or affiliate programme
- [ ] In-app feedback widget or NPS survey
- [ ] Custom domains (if applicable)
- [ ] White-labelling (if applicable)

### Advanced infrastructure

- [ ] Distributed tracing / APM
- [ ] Log aggregation and search
- [ ] CDN for static assets
- [ ] Database read replicas (if needed for scale)

### Internationalisation

- [ ] Multi-language support
- [ ] Timezone-aware date display
- [ ] Localised pricing and currency

---

## Common Launch Mistakes

### Skipping legal pages

You cannot legally accept payments without a Privacy Policy and Terms of Service. These are not optional. They do not need to be perfect, but they must exist and be linked from the payment flow.

### Not setting up billing webhooks

Stripe and most payment providers use webhooks to notify your application of events (payment succeeded, payment failed, subscription cancelled). If these are not handled, your application will have no idea that a payment failed and will continue giving access to non-paying customers, or will cancel access for customers who have paid.

### Launching with debug mode on

Many frameworks (Laravel, Django, Rails) have a debug mode that shows stack traces to the user on errors. This exposes sensitive information and is a security risk. Always verify debug mode is off before going to production.

### No error tracking

You will not know about bugs in production unless something tells you. Set up error tracking before launch, not after. You want to know about errors before your users email you about them.

### Empty states that are genuinely empty

A new user who signs up and sees a blank page with no guidance will leave. Every section of the application needs a meaningful empty state with a clear first action.

### No monitoring

If the application goes down or a service degrades, you need to know before your users notice. Basic uptime monitoring (every 5 minutes) and a health check endpoint take 30 minutes to set up and save significant user trust.
