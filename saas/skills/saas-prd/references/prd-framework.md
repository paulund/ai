# SaaS PRD Framework

This document contains reusable PRD templates for the 19 standard SaaS feature categories. Copy relevant sections into your product PRD and fill in the decisions.

Most of what you build in a SaaS product is not your actual product idea — it is the infrastructure around it. Documenting these features once in a reusable framework eliminates the need to rediscover the same decisions on every new project.

---

## 1. Authentication

### Decisions to make
- [ ] Email/password only, or include OAuth providers?
- [ ] Which OAuth providers? (Google, GitHub, Microsoft, Apple)
- [ ] Allow account creation with conflicting email (same email, different OAuth provider)?
- [ ] Email verification required before login, or after?
- [ ] Session duration: how long before auto-logout?
- [ ] 2FA: optional or required, and for which tiers?
- [ ] API token authentication: yes/no?
- [ ] SSO/SAML: which tier, and which providers? (Okta, Azure AD, Google Workspace)

### Requirements

#### Email and password
- User can register with email and password
- Password must meet minimum complexity requirements
- Password reset via email link (expires after 24 hours)
- Failed login attempts trigger rate limiting after [N] attempts

#### OAuth
- OAuth sign-in with [providers]
- Handle account conflict: existing email account with new OAuth provider — [merge accounts / block / ask user]
- New OAuth signup creates account automatically

#### Session management
- Sessions expire after [N] days of inactivity
- User can view active sessions
- User can revoke individual sessions

#### Two-factor authentication
- TOTP support (Google Authenticator, Authy)
- Backup codes generated at setup (10 single-use codes)
- Recovery flow for lost device

#### API tokens
- User can create named API tokens
- Tokens displayed once at creation, stored as hash
- User can revoke individual tokens

#### SSO/SAML (enterprise tier only)
- Organisation admin configures SSO provider
- Just-in-time provisioning: new users created on first SSO login
- SSO bypass option for admins if SSO provider fails

---

## 2. Team & Organisation Management

### Decisions to make
- [ ] Organisations: single-level (org + members) or multi-level (org + teams + members)?
- [ ] Roles: owner/admin/member, or more granular?
- [ ] Seat-based billing: yes/no?
- [ ] Invite flow: invites by email, or shareable link, or both?
- [ ] What happens to content when a member is removed?

### Requirements

#### Organisation creation
- User creates organisation with a name during onboarding
- Organisation has a unique slug/URL if applicable

#### Inviting members
- Admin sends invitation by email
- Invitation email contains a single-use link (expires after [N] days)
- Existing user accepts → joined to org; New user → account creation then join
- Admin can resend or revoke pending invitations

#### Roles and permissions
- Owner: full access including billing, can transfer ownership
- Admin: can manage members and settings, cannot access billing
- Member: standard access, cannot manage members

#### Member removal
- Admin removes member → access revoked immediately
- Removed member's content: [retained by org / transferred to admin / deleted]

#### Ownership transfer
- Owner can transfer ownership to another member
- Confirmation required from both parties

---

## 3. Subscription Tiers & Billing

### Decisions to make
- [ ] What tiers? (Free + Paid / Free + Pro + Enterprise / Trial + Paid / Paid only)
- [ ] Billing intervals: monthly only, or monthly and annual?
- [ ] Annual discount amount?
- [ ] Trial length, and what happens at expiry?
- [ ] Which payment provider? (Stripe is standard)
- [ ] Upgrade path: immediate access or at next billing cycle?
- [ ] Downgrade path: immediate or at end of billing period?
- [ ] Cancellation: immediate or end of period?
- [ ] Data retention after cancellation: how long?
- [ ] Failed payment: how many retries, what happens to account access?

### Tier structure

Document each tier with:
- Name
- Monthly price
- Annual price (if applicable)
- Feature access
- Usage limits
- Support level

### Feature gating

For each premium feature, document:
- Which tiers can access it?
- What happens when a free user tries to access it? (Upgrade prompt / error / disabled UI)
- What happens when a paid user hits a limit? (Upgrade prompt / hard block / graceful degradation)

---

## 4. Email Flows

Every SaaS product needs these transactional emails. Document each one before building.

For each email, define: trigger, recipient, subject line, and content summary.

| Email | Trigger | Required? |
| --- | --- | --- |
| Welcome | Account created | Yes |
| Email verification | Account created (if verification required) | Yes |
| Password reset | User requests reset | Yes |
| Password changed | Password successfully changed | Recommended |
| Team invitation | Admin invites member | Yes (if teams) |
| Subscription created | Paid plan activated | Yes |
| Subscription cancelled | Subscription cancelled | Yes |
| Subscription renewed | Billing cycle renewed | Recommended |
| Payment failed | Payment attempt fails | Yes |
| Trial ending (7 days) | 7 days before trial ends | Recommended |
| Trial ending (3 days) | 3 days before trial ends | Recommended |
| Trial expired | Trial period ended | Recommended |
| Account deletion | User deletes account | Recommended |

---

## 5. Legal & Compliance

### Decisions to make
- [ ] Host legal pages yourself or use a service (Termly, Iubenda)?
- [ ] Cookie consent required? (Yes if serving EU users)
- [ ] Acceptable Use Policy needed? (Yes if user-generated content)
- [ ] DPAs available for B2B customers?

### Requirements

#### Required pages
- Terms of Service — must exist before accepting payments
- Privacy Policy — must describe data collection, storage, and deletion
- Cookie Policy — required for EU users if using tracking cookies
- Acceptable Use Policy — required if users create or upload content

#### Cookie consent (if applicable)
- Cookie consent banner on first visit
- Preferences stored in browser
- Analytics/tracking only loaded after consent
- Consent can be withdrawn

---

## 6. User Account Settings

### Requirements

#### Profile
- User can update: display name, profile photo
- Changes saved immediately

#### Email address
- User can change email address
- New email requires verification before change takes effect
- Old email receives notification of the change

#### Password
- User can change password (requires current password)
- Password change invalidates all other sessions

#### Two-factor authentication
- User can enable/disable 2FA
- Setup flow: scan QR code, verify with code, save backup codes
- Recovery: use backup code if device is lost

#### Active sessions
- User can view all active sessions with device/location info
- User can revoke individual sessions or all sessions except current

#### Account deletion
- User can request account deletion
- Confirmation required (type email or "DELETE")
- [Immediate deletion / 30-day grace period with reactivation]
- Notification email sent confirming deletion

---

## 7. Notifications

### Decisions to make
- [ ] In-app notification centre: yes/no?
- [ ] Which events trigger notifications?
- [ ] Per-notification preferences (user can turn off specific types)?
- [ ] Email and/or in-app, or just one?

### Requirements

#### In-app notification centre
- Bell icon with unread count badge
- Notification list with newest first
- Mark individual or all notifications as read
- Notification types: [system / billing / team / product-specific]

#### Email notifications
- User can configure: which notifications trigger email
- Global email unsubscribe option
- Transactional emails cannot be unsubscribed (welcome, password reset)

#### Retention
- Notifications retained for [N] days then archived or deleted

---

## 8. Data Export & Privacy

### Decisions to make
- [ ] What data can users export? (All data / specific data types)
- [ ] Export format: JSON, CSV, ZIP?
- [ ] Synchronous (immediate) or asynchronous (email link when ready)?
- [ ] Account deletion: immediate purge or soft delete?
- [ ] Data anonymisation option, or just deletion?

### Requirements

#### Data export
- User can request full account data export
- [Immediate download / asynchronous generation with email link]
- Export includes: account data, [product-specific content], billing history

#### Account and data deletion
- User requests deletion through account settings
- [All data deleted immediately / 30-day soft delete with reactivation available]
- Confirmation email sent with deletion date

#### GDPR (if serving EU users)
- Privacy Policy describes all data processed and legal basis for processing
- Right to access: user can export all personal data
- Right to erasure: user can delete their account and data
- DPAs available for B2B customers on request

---

## 9. API & Webhooks

### Decisions to make
- [ ] Public API: which tier has access?
- [ ] API versioning strategy: URL versioning (/v1/), header versioning?
- [ ] Rate limiting: per key, per tier, or both?
- [ ] Outbound webhooks: yes/no?
- [ ] Which events trigger webhooks?

### Requirements

#### API keys
- User can create named API keys with optional scope selection
- Key displayed once on creation, stored as hash
- User can revoke individual keys
- Rate limits applied per key and per tier

#### API versioning
- Versioned API endpoints (e.g., `/api/v1/`)
- Deprecation notices with minimum 6 months sunset period

#### Webhooks
- User subscribes to specific event types
- User provides endpoint URL and receives a signing secret
- Webhook delivery: HTTPS only, minimum TLS 1.2
- Retry logic: [N] retries with exponential backoff
- Delivery logs: success/failure, request/response body, timestamp
- Signature verification: HMAC-SHA256 of payload using signing secret

---

## 10. Audit Log

### Decisions to make
- [ ] Which tier includes audit log access?
- [ ] What events are logged?
- [ ] Retention period (often extended for enterprise)?
- [ ] Export capability?

### Events to log (as applicable)
- Authentication: login, logout, failed attempts, 2FA events
- Account: email change, password change, account deletion
- Team: member invited, joined, removed, role changed
- Billing: plan changed, payment succeeded/failed, subscription cancelled
- API: token created, token revoked
- Admin actions: any action taken by internal admin

### Requirements
- Events show: timestamp, user, action, affected resource, IP address
- Immutable log — entries cannot be modified or deleted by users
- Filterable by event type, user, date range
- Export as CSV or JSON

---

## 11. Infrastructure & Monitoring

### Decisions to make
- [ ] Error tracking: Sentry, Bugsnag, or custom?
- [ ] Uptime monitoring: Better Uptime, Pingdom, or custom?
- [ ] Status page: Instatus, Statuspage.io, or custom?
- [ ] Log aggregation: Papertrail, Logtail, Datadog, or cloud-native?
- [ ] Analytics: Plausible, PostHog, or standard platforms?
- [ ] Email deliverability: SPF, DKIM, DMARC configured?

### Requirements

#### Error tracking
- All unhandled exceptions captured and alerted
- Error rate alerts configured for production
- Source maps or stack traces available for debugging

#### Health checks
- `/health` endpoint returns 200 when all services are healthy
- Checks: database connectivity, cache connectivity, queue processing
- Uptime monitoring pings health endpoint every [N] minutes

#### Status page
- Public status page showing current status of major components
- Incident creation and communication workflow
- Historical uptime visible

#### Email deliverability
- SPF record configured
- DKIM signing enabled
- DMARC policy set
- Sending domain warmed if new

---

## 12. Customer Support

### Decisions to make
- [ ] Support channels: email only, live chat, or both?
- [ ] Helpdesk tool: Intercom, Crisp, Zendesk, Help Scout, Linear?
- [ ] Documentation platform: internal wiki, Mintlify, GitBook?
- [ ] SLA by tier: response time commitments per plan?

### Requirements
- Support email or in-app chat available from every page
- Help documentation accessible without login
- SLA commitments documented per tier (e.g., free: best effort; pro: 24h; enterprise: 4h)

---

## 13. Onboarding

### Decisions to make
- [ ] Wizard-style onboarding steps, or self-directed with empty states?
- [ ] Welcome modal on first login: yes/no?
- [ ] Sample/demo content created automatically on signup?
- [ ] Email onboarding sequence: how many emails, over what period?

### Requirements

#### Empty states
- Every empty list or dashboard has a meaningful empty state with a clear CTA
- Empty state copy explains what goes here and how to add it

#### Getting started
- New user sees a getting-started checklist or progress indicator
- Steps: [product-specific, e.g., create first [thing], invite a team member, connect integration]
- Checklist dismissible once all steps are complete

#### Welcome sequence
- Email 1 (immediate): welcome, next step, how to get help
- Email 2 (day 2): key feature highlight
- Email 3 (day 5): use case or customer story
- Email 4 (day 10): reminder if core action not completed

---

## 14. Admin Panel

### Decisions to make
- [ ] Build custom or use a framework (Filament for Laravel is recommended)?
- [ ] Which internal users have access?
- [ ] Which user data can internal admins view/modify?

### Requirements

#### User management
- Search and view all user accounts
- View account details: plan, signup date, last active, subscription status
- Ability to impersonate a user for debugging
- Ability to manually update subscription or extend trial

#### Subscription management
- Override plan for any user
- Manually apply credits or discounts
- View and export billing history

#### Monitoring dashboard
- Active users (today, 7d, 30d)
- New signups
- Active subscriptions and MRR
- Recent errors

---

## 15. Feature Flags

### Decisions to make
- [ ] Build flags into the application, or use a service (LaunchDarkly, Unleash, Flipt)?
- [ ] Who can manage flags? (Developers only / admin panel)

### Requirements

- Feature can be enabled/disabled globally without a deployment
- Feature can be enabled for specific users or a percentage rollout
- Tier-based defaults: some features enabled by tier, overrideable per user
- Admin panel UI for managing flags (if non-developer access needed)

---

## 16. Custom Domains

### Decisions to make
- [ ] Subdomain only (user.yourapp.com), or full custom domain (app.theirdomain.com)?
- [ ] Which tier includes custom domains?
- [ ] DNS verification method: CNAME, TXT record?
- [ ] SSL provisioning: Let's Encrypt automated, or manual?

### Requirements

- User adds their domain in settings
- System provides DNS record to configure
- System verifies DNS propagation before activating
- SSL certificate automatically provisioned and renewed
- Custom domain serves the same application at the correct account

---

## 17. White-Labelling

### Decisions to make
- [ ] Which tier has white-labelling?
- [ ] Scope: logo and colours only, or also custom email domain and "powered by" removal?
- [ ] How is it priced: tier feature or add-on?

### Requirements

- Organisation can upload logo replacing default branding
- Primary colour configurable
- Transactional emails sent from custom domain (if in scope)
- "Powered by [Product]" footer removed

---

## 18. Internationalisation

### Decisions to make
- [ ] Which languages to support at launch?
- [ ] Timezone: store in UTC and display in user's timezone?
- [ ] Currency: USD only, or localised pricing?
- [ ] Right-to-left language support needed?

### Requirements

- All user-facing strings externalised to translation files
- User can set preferred language and timezone in account settings
- Dates displayed in user's local timezone
- Currency formatted according to user locale

---

## 19. Marketing & Growth Infrastructure

### Requirements

#### Pre-launch
- [ ] Landing page with email capture
- [ ] Waitlist incentive defined
- [ ] Domain and SSL configured
- [ ] Analytics configured (privacy-friendly preferred: Plausible, Fathom)

#### Content and SEO
- [ ] Blog or notebook for long-form content
- [ ] Core landing pages for primary keywords
- [ ] Sitemap generated and submitted to Search Console
- [ ] Meta titles and descriptions on all public pages
- [ ] Branded 404 and 500 error pages

#### Post-launch
- [ ] Changelog page (what changed, when — builds trust)
- [ ] Documentation accessible without login
- [ ] Affiliate or referral programme (if applicable)
- [ ] Customer feedback mechanism (in-app widget, NPS survey)
- [ ] Social media presence established before launch
