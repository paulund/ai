---
name: saas-launch-checklist
description: Use when auditing launch readiness for a SaaS product, doing a pre-launch review, or checking what is missing before going live. Produces a categorised checklist with status for each item and clear distinction between must-have blockers and post-launch additions.
---

## Core Workflow

1. **Ask about the product** — what it does, B2B or B2C, solo or team-based, paid or freemium.
2. **Ask what is already built** — the user describes current state. Do not assume anything is done.
3. **Load the launch readiness reference** — read `references/launch-readiness.md` for the full checklist.
4. **Run the audit** — work through each category and assess status based on what the user has described.
5. **Output the checklist** — for each item, assign a status: `Done`, `In Progress`, `Needed`, or `Optional`. Mark blockers clearly.
6. **Summarise blockers** — at the end, list the items that must be completed before launch, separate from everything else.

---

## Launch Priority Tiers

Not everything needs to be done before launch. Use these tiers to guide prioritisation:

### Must-have before launch

These are blockers. Do not launch without them.

- **Authentication** — users must be able to sign up and sign in
- **Billing** — if charging money, payment flow must work end-to-end (including webhooks for failed payments)
- **Core email flows** — welcome email, password reset, email verification (at minimum)
- **Legal pages** — Terms of Service and Privacy Policy must exist before accepting payments
- **Basic error monitoring** — you need to know when things break in production
- **Secure configuration** — no secrets in code, HTTPS enforced, no debug mode in production

### Should-have for launch

Important but not blockers. Ship these in the first 2-4 weeks post-launch.

- **Onboarding flow** — empty states, getting started guide, welcome sequence
- **User account settings** — profile editing, password change, account deletion
- **Branded error pages** — 404 and 500 pages that do not expose stack traces
- **Support channel** — email address or helpdesk people can reach
- **Subscription management** — ability to upgrade, downgrade, or cancel
- **Health check endpoint** — for uptime monitoring

### Can add post-launch

These are real features but rarely needed on day one. Add when the user base demands them.

- Team and organisation management
- API and webhook support
- MCP server (AI assistant integration)
- Audit log
- Custom domains
- Admin panel
- Feature flags
- White-labelling
- Internationalisation
- In-app notification centre
- Data export / GDPR tooling
- SSO / SAML

---

## Questions to Ask Before Running the Audit

These answers determine which items are blockers vs optional:

1. Are you charging money at launch, or starting free?
2. Is this B2B (multiple users per account) or B2C (solo users)?
3. Are you targeting enterprise buyers, or self-serve?
4. Is this available to users in the EU? (Affects GDPR requirements)
5. What is the launch date target?
6. Do you plan to offer an MCP server for AI assistant integration? (Developer-facing products benefit from this early)

---

## Output Format

Present the audit as a table or checklist grouped by priority tier:

```markdown
## Must-have before launch

| Item | Status | Notes |
| --- | --- | --- |
| User signup and login | Done | |
| Email/password auth | Done | |
| OAuth (Google/GitHub) | Needed | User expects it |
| Password reset flow | Done | |
| Stripe integration | In Progress | Payment works, webhooks not set up |
| Failed payment handling | Needed | Blocker |
| Welcome email | Needed | Blocker |
| Terms of Service | Done | |
| Privacy Policy | Needed | Blocker |
| Error monitoring (Sentry) | Not started | Blocker |

## Blockers summary

Before launch, complete:
1. Set up Stripe webhooks for failed payment handling
2. Write and publish Privacy Policy
3. Set up Sentry (or equivalent) error monitoring
4. Add welcome email to signup flow
```

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| Launch Readiness Checklist | `references/launch-readiness.md` | Always |

---

## Constraints

### MUST DO

- Ask about product type and what is already built before auditing
- Distinguish clearly between blockers and nice-to-haves
- Tailor the checklist to the product — B2B needs different things than B2C
- Summarise blockers at the end as a clear action list
- Mark items as Optional when they genuinely are not needed for this product type

### MUST NOT DO

- Apply every item to every product — enterprise features are not needed for consumer apps
- List items without assigning a status
- Treat "should-have" items as blockers
- Skip the context-gathering questions and make assumptions
- Present hundreds of items without prioritisation — the value is in what to do first
