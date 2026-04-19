---
name: saas-prd
description: Use when generating PRDs for standard SaaS features, planning feature requirements, or documenting decisions for authentication, billing, teams, email flows, legal, notifications, API, and other table-stakes SaaS functionality. Use this before starting implementation to document the decisions that vary per project.
---

## Core Workflow

1. **Ask which categories are relevant** — show the user the 19 standard SaaS feature categories and ask which ones they want to document. Or ask about the product type and make a recommendation.
2. **Ask product-level context questions** — B2B or B2C? Solo users or team-based? Free tier or paid-only? These answers shape the decisions within each category.
3. **Load the PRD framework** — read `references/prd-framework.md` for the full template for each category.
4. **Generate PRD sections** — for each requested category, produce a PRD section with decisions already made where context makes them clear, and explicit decision prompts where they vary by product.
5. **Highlight the decisions** — for each category, explicitly call out the choices that need to be made. The value is in surfacing what to decide, not just listing what features exist.
6. **Output inline or to file** — ask whether the user wants the PRD inline in the conversation or saved to a specified file path.

---

## The 19 Standard SaaS Feature Categories

Every SaaS product needs most of these. Documenting them up front prevents repeated deliberation during development.

| # | Category | Why it matters |
| --- | --- | --- |
| 1 | Authentication | Decisions here affect security, UX, and enterprise sales |
| 2 | Team & Organisation Management | Required for B2B; shapes billing model and permissions |
| 3 | Subscription Tiers & Billing | Core to revenue; many edge cases to decide early |
| 4 | Email Flows | 11+ transactional emails needed; easy to forget until launch |
| 5 | Legal & Compliance | Non-negotiable before accepting payments |
| 6 | User Account Settings | Profile, password, 2FA, sessions, deletion |
| 7 | Notifications | In-app and email notification preferences |
| 8 | Data Export & Privacy | GDPR requirements; needed for B2B sales |
| 9 | API & Webhooks | Required for integration and enterprise tier |
| 10 | Audit Log | Often required for enterprise/compliance buyers |
| 11 | Infrastructure & Monitoring | Error tracking, health checks, status page |
| 12 | Customer Support | Channel, SLAs, helpdesk tooling |
| 13 | Onboarding | Empty states, getting started, activation |
| 14 | Admin Panel | Internal tooling for user management |
| 15 | Feature Flags | Per-user overrides, tier defaults |
| 16 | Custom Domains | Enterprise/white-label feature |
| 17 | White-Labelling | Remove branding for enterprise resellers |
| 18 | Internationalisation | Multi-language, timezone, currency |
| 19 | Marketing & Growth Infrastructure | Launch plan, blog, SEO, changelog, referrals |

---

## PRD Section Format

For each category, produce a PRD section in this structure:

```markdown
## [Category Name]

### Overview
[1-2 sentences on what this covers and why it is in scope]

### Decisions to make
- [ ] [Decision 1]: [Options] — default recommendation: [option]
- [ ] [Decision 2]: [Options] — depends on: [context]

### Requirements

#### [Sub-feature 1]
- [Specific requirement]
- [Specific requirement]

#### [Sub-feature 2]
- [Specific requirement]
```

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| PRD Framework | `references/prd-framework.md` | Always — contains full templates for all 19 categories |

---

## Constraints

### MUST DO

- Ask which categories to document before generating anything
- Ask product-level context questions (B2B/B2C, team-based, free tier) that affect decisions
- Explicitly surface the decisions that need to be made within each category
- Tailor the PRD to the product — a solo-user B2C product does not need SSO; a B2B product does
- Output clean markdown formatted PRD sections

### MUST NOT DO

- Generate PRDs for all 19 categories without asking which are relevant
- Write generic feature lists without surfacing decisions
- Assume enterprise features (SSO, SAML, audit log) are needed for every product
- Assume solo features (no team management) when the product is team-based
- Make decisions the user should make — surface them, do not hide them
