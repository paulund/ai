# Page templates + quiz packs

Each section below has **two parts**:

1. **Quiz for this page** — the questions to ask the user via the `question` tool before writing the file. Adapt wording to whatever the user already answered in earlier sections; don't re-ask things they already said.
2. **Template** — the structural starting point for the file. Replace placeholders with real answers from the quiz. If the user skipped a question, leave a `<!-- TODO: fill -->` marker rather than inventing.

The skill targets this tree (mirrors SKILL.md "Target structure"):

```
docs/
├── README.md
├── product/      overview, features, pricing, onboarding, supported-platforms
├── architecture/ overview, data-model, auth, api-design, mcp/*
├── deploy/       environments, ci-cd, rollback, <platform>.md
├── operations/   runbook, observability, cron-jobs, cost
├── security/     threat-model, secrets, data-handling, compliance
├── business/     support-model, legal/{privacy-policy, terms, dpa}
├── testing/      strategy, fixtures, mocking-services
├── contributing/ pr-conventions, code-review-checklist, release-process
├── reference/    glossary, env-vars, error-codes, events
└── adr/          NNN-<title>.md (Nygard format)
```

---

## docs/product/overview.md

### Quiz

- One-sentence product description?
- Primary user persona (one)?
- Single most important job-to-be-done?
- What does this product NOT do (boundary)?

### Template

```markdown
# Product Overview

## What it is

<one-sentence product description>

## Who it's for

<primary user persona, with concrete context — what's their job, what's their pain>

## Core job-to-be-done

<the single most important thing the user accomplishes>

## What this product is NOT

<explicit boundaries — what it doesn't try to be>
```

---

## docs/product/features.md

### Quiz

- List shipped features (one line each)?
- Anything in beta or behind a flag?
- Anything planned for next quarter?

### Template

```markdown
# Features

## Shipped

- <feature name> — <one-line description>. <link to relevant doc or ADR>.
- ...

## In beta / behind a flag

- <feature> — <one-line description>. <flag name> to enable.

## Planned (next quarter)

- <feature> — <one-line description>. <issue link>.

## Explicitly NOT supported

- <thing> — <reason>.
```

---

## docs/product/pricing.md

### Quiz

- Tiers and prices?
- Limits per tier (posts/mo, channels, etc.)?
- Trial policy?
- Payment provider?
- Where does billing UI live?

### Template

```markdown
# Pricing

## Tiers

| Tier  | Price       | Limits                              | Target user           |
| ----- | ----------- | ----------------------------------- | --------------------- |
| Free  | $0          | <n> posts/mo, <n> channels          | <persona>             |
| Pro   | $<x>/mo     | <n> posts/mo, <n> channels          | <persona>             |
| Team  | $<x>/user/mo| <n> posts/mo, <n> channels          | <persona>             |

## Trial policy

<none / <n>-day trial / freemium / etc>

## Payment provider

<Stripe / Paddle / Lemon Squeezy / …> — webhook URL, billing portal link.

## Where the billing UI lives

<path to settings/billing route>
```

---

## docs/product/onboarding.md

### Quiz

- First-run flow (3–5 steps)?
- Required vs optional setup?
- Empty-state handling?

### Template

```markdown
# Onboarding

## First-run flow

1. <step> — <what the user does, what the app does>
2. <step>
3. <step>

## Required setup

- <thing> — <why it's required>

## Optional setup

- <thing> — <why it's nice to have>

## Empty states

- <state> — <what the user sees, what they should do>
```

---

## docs/product/supported-platforms.md

### Quiz

- Which external integrations are supported?
- Which are planned?

### Template

```markdown
# Supported platforms

| Platform    | Status     | Notes                                  |
| ----------- | ---------- | -------------------------------------- |
| <platform>  | shipped    | <one line — what it can do>            |
| <platform>  | planned Q? | <what's missing>                       |
```

---

## docs/architecture/overview.md

### Quiz

- One-paragraph system map: client → edge → app → DB → third parties?
- Primary framework / runtime?
- Why this stack (link ADR)?

### Template

```markdown
# Architecture overview

## System map

<one-paragraph description: client → edge → app → DB → third parties>

## Stack

- **Framework:** <Next.js / Remix / Express / …>
- **Runtime:** <Cloudflare Workers / Node / Bun / …>
- **DB:** <Postgres / SQLite / …>
- **ORM:** <Drizzle / Prisma / …>
- **Auth:** <provider>

## Why this stack

<paragraph or link to ADR — usually the "data fetching architecture" ADR>
```

---

## docs/architecture/data-model.md

### Quiz

- Top 5 entities?
- Tenancy strategy (single-tenant / multi-tenant / project-scoped)?
- Soft-delete vs hard-delete?
- Who owns schema migrations?

### Template

```markdown
# Data model

## Tenancy

<single-tenant / multi-tenant / project-scoped / org-scoped> — link to ADR if decision was deliberate.

## Entities (top-level)

| Entity        | Owns                 | Key relationships               |
| ------------- | -------------------- | ------------------------------- |
| <Entity>      | <what owns it>       | <has many X, belongs to Y>      |
| ...           | ...                  | ...                             |

## Schema migrations

- ORM: <Drizzle / Prisma / …>
- Where schemas live: <path>
- Migration command: `<command>`
- Migration rules: <see ADR NNN>

## Soft-delete vs hard-delete

<strategy + rationale>

## Audit log

<what's audited, where it lives>
```

---

## docs/architecture/auth.md

### Quiz

- Session strategy (cookies / tokens / both)?
- API key model (user-scoped / project-scoped / org-scoped)?
- OAuth providers connected?
- Where does auth UI live?

### Template

```markdown
# Authentication & Authorization

## Session strategy

<cookies / tokens / both> — link to ADR.

## API keys

<user-scoped / project-scoped / org-scoped> — how issued, how rotated, how revoked.

## OAuth providers

| Provider       | Use case                  | Callback URL               |
| -------------- | ------------------------- | -------------------------- |
| <provider>     | <user login / channel>    | <callback path>            |

## Authorization model

<RBAC / ABAC / simple ownership check> — which roles exist, what they can do.

## Where auth UI lives

<path to login / settings / API keys page>
```

---

## docs/architecture/api-design.md

### Quiz

- REST or RPC?
- Response envelope (envelope vs raw)?
- Pagination style (cursor / offset / link header)?
- Error code format?

### Template

```markdown
# API design

## Protocol

<REST / RPC / hybrid> — link to ADR.

## Response envelope

\`\`\`json
{
  "data": { ... },
  "error": { "code": "...", "message": "..." },
  "meta": { "requestId": "...", "timestamp": "..." }
}
\`\`\`

## Pagination

<cursor-based / offset / Link header>

## Error codes

<stable format e.g. `DOMAIN_ACTION_REASON` — link to docs/reference/error-codes.md>

## Versioning

<URL prefix / header / none>

## Rate limiting

<strategy, headers, response on exceed>
```

---

## docs/architecture/mcp/*

Only ask the user about this section if an MCP server exists. Otherwise skip.

### Quiz

- Which tools exist?
- Approval gate pattern (confirmed: true)?
- Client compatibility targets?

### Template

See existing `docs/mcp/*.md` for the Syndly pattern. Each section needs:
- `overview.md` — what the MCP server does
- `getting-started.md` — connect your agent in 5 minutes
- `tools-reference.md` — full tool list with input schemas
- `architecture.md` — auth model, transport, approval gate
- `client-compatibility.md` — Claude Code / Cursor / Codex CLI config

---

## docs/deploy/environments.md

### Quiz

- List environments (dev / staging / prod / preview)?
- How does each get a deploy (branch / PR / manual)?
- Database per environment?

### Template

```markdown
# Environments

| Environment | URL                  | Branch / trigger      | DB                  | Deploys to       |
| ----------- | -------------------- | --------------------- | ------------------- | --------------- |
| dev         | localhost            | manual                | local / shared dev  | local           |
| preview     | <*.preview.example>  | PR                    | ephemeral branch DB | <staging infra> |
| staging     | <staging.example>    | main                  | staging DB          | <staging infra> |
| prod        | <example.com>        | tag / manual          | prod DB             | <prod infra>    |

## Database per environment

<shared / per-env / per-branch>
```

---

## docs/deploy/<platform>.md

### Quiz

- Hosting platform?
- Paid-tier requirement (and why)?
- Bundle size budget?
- Cold-start budget?
- One-time setup steps?

### Template

```markdown
# Deploying to <Platform>

## Prerequisites

- <account tier requirement>
- <bundle size budget>
- <cold-start budget>

## Build pipeline

`<build command>` runs:
1. <step>
2. <step>

## One-time setup

1. <step>
2. <step>

## Environment variables

<list every var, where it's set>

## Deploy command

`<command>`

## Rollback

<command or procedure>
```

---

## docs/deploy/ci-cd.md

### Quiz

- CI provider?
- Pre-deploy gates (which commands must pass)?
- Migrations: when applied?

### Template

```markdown
# CI/CD

## Provider

<GitHub Actions / GitLab CI / CircleCI / …>

## Pre-deploy gates

Every PR must pass:
- `<command>`
- `<command>`
- `<command>`

## Migrations

Applied <manually / on deploy / on tag / via CI step>. See ADR NNN.

## Deploy trigger

<auto on main / manual / on tag>
```

---

## docs/deploy/rollback.md

### Quiz

- Rollback command?
- DB rollback strategy (forward-fix only / reversible migrations)?
- Feature-flag kill-switch list?

### Template

```markdown
# Rollback

## Roll back app

`<command>` — what it does.

## Roll back DB

<forward-fix only / reversible migrations / restore from snapshot>

## Feature-flag kill-switches

| Flag                          | What it kills                          |
| ----------------------------- | -------------------------------------- |
| `<flag_name>`                 | <description>                          |
```

---

## docs/operations/runbook.md

### Quiz

- Where do errors surface (dashboard / log sink / pager)?
- On-call rotation?
- Top 3 incidents and how they were resolved?
- Status page?

### Template

```markdown
# Runbook

## Where do errors go?

<log sink / dashboard URL>

## On-call

<rotation, paging tool, escalation>

## Top incidents (last 90 days)

### YYYY-MM-DD: <one-line title>
- **Impact:** <what users saw>
- **Root cause:** <one paragraph>
- **Fix:** <link to PR>
- **Detection time / resolution time:** <duration>

## Common pages

- "Posts failing to publish" → see <runbook section>
- "Webhook deliveries stalling" → see <runbook section>

## Status page

<URL>
```

---

## docs/operations/observability.md

### Quiz

- Logs: where do they go, structured?
- Metrics: what matters?
- Traces: enabled or no?

### Template

```markdown
# Observability

## Logs

<where they go, structured JSON yes/no, retention>

## Metrics

<what matters — RPS, error rate, p95 latency, queue depth, …>

## Traces

<enabled / no, sampling rate, which services>
```

---

## docs/operations/cron-jobs.md

### Quiz

- List scheduled jobs (name, schedule, owner, what it does)?
- Concurrency / dedup strategy?

### Template

```markdown
# Cron jobs

| Name           | Schedule      | Owner   | What it does                  | Concurrency  |
| -------------- | ------------- | ------- | ----------------------------- | ------------ |
| <job>          | `*/5 * * * *` | <name>  | <one line>                    | <single/parallel> |
```

---

## docs/operations/cost.md

### Quiz

- Hosting cost / mo?
- DB cost / mo?
- Top 3 cost drivers?

### Template

```markdown
# Cost

## Monthly run-rate

| Item                | $/mo  | Trend   |
| ------------------- | ----- | ------- |
| Hosting             | <$x>  | <↑/↓/→> |
| Database            | <$x>  | <↑/↓/→> |
| Third-party APIs    | <$x>  | <↑/↓/→> |

## Top 3 cost drivers

1. <driver> — <why it's expensive>
2. <driver>
3. <driver>

## Budgets & alerts

<where the alert fires, who gets paged>
```

---

## docs/security/threat-model.md

### Quiz

- What are we protecting (data, money, reputation)?
- Top 3 threats (STRIDE-lite)?
- Authentication weaknesses?
- Data-exfiltration risks?

### Template

```markdown
# Threat model

## What we protect

- **Data:** <list PII / sensitive categories>
- **Money:** <Stripe customer data, billing state>
- **Reputation:** <OAuth tokens, brand accounts>

## Top threats (STRIDE-lite)

| Threat                                  | Likelihood | Impact | Mitigation                          |
| --------------------------------------- | ---------- | ------ | ----------------------------------- |
| <e.g. stolen API key>                   | Medium     | High   | <hashed, scoped, revocable>         |
| <e.g. SSRF via webhook URL>             | Low        | High   | <URL allowlist, no internal routing>|
| ...                                     | ...        | ...    | ...                                 |

## Authentication weaknesses

<session fixation / CSRF / token leakage / etc — link to docs/architecture/auth.md>

## Data exfiltration risks

<dump DB / leak logs / accidentally public S3 — and what stops each>

## Out of scope

<advanced persistent threats, nation-state, etc — explicitly>
```

---

## docs/security/secrets.md

### Quiz

- Where are secrets stored (env / vault / KMS)?
- Rotation policy?
- Who has access?

### Template

```markdown
# Secrets

## Where secrets live

| Env var         | Storage                  | Rotation     |
| --------------- | ------------------------ | ------------ |
| <NAME>          | <wrangler secret / .env> | <manual / auto> |

## Rotation policy

<cadence, owner, process>

## Access

<who can read / write — list roles, link to admin tooling>

## Incident: leaked secret

1. Revoke immediately.
2. Rotate.
3. Audit usage logs for the leak window.
4. <postmortem template>
```

---

## docs/security/data-handling.md

### Quiz

- PII fields (list)?
- Encryption at rest (which columns)?
- Encryption in transit?
- Data retention policy?

### Template

```markdown
# Data handling

## PII fields

| Field      | Table     | Sensitivity | Handling                  |
| ---------- | --------- | ----------- | ------------------------- |
| <field>    | <table>   | <high/med/low> | <encrypt, redact, etc>  |

## Encryption at rest

<which columns, which algorithm>

## Encryption in transit

<TLS version, certificate rotation>

## Data retention

<how long, when purged, who can request early delete>
```

---

## docs/security/compliance.md

### Quiz

- GDPR posture?
- SOC2 posture?
- Other (HIPAA, PCI)?

### Template

```markdown
# Compliance

## GDPR

<data subject access requests process, right to deletion, data export>

## SOC2

<in progress / not started / certified — link to audit docs>

## Other

<HIPAA / PCI / … if relevant>
```

---

## docs/business/support-model.md

### Quiz

- Support channels (email / chat / Discord)?
- SLA targets (response / resolution)?
- Escalation path?

### Template

```markdown
# Support model

## Channels

- <channel> — <when to use>

## SLA

| Tier     | First response | Resolution target |
| -------- | -------------- | ----------------- |
| <tier>   | <duration>     | <duration>        |

## Escalation

1. <step>
2. <step>
3. <step>
```

---

## docs/business/legal/privacy-policy.md, terms.md, dpa.md

### Quiz

- Existing document to link to? Generate placeholder?

### Template

If no existing document, generate a placeholder with a clear `<!-- TODO: replace with legal-reviewed text -->` marker and a "last reviewed by counsel" placeholder.

```markdown
# <Document title>

<!-- TODO: replace with legal-reviewed text -->

_Last reviewed by counsel: YYYY-MM-DD_
```

---

## docs/testing/strategy.md

### Quiz

- Unit / integration / e2e split (when to use each)?
- Decision tree?
- Coverage targets?
- What must NEVER be untested?

### Template

```markdown
# Testing strategy

## Layers

| Layer        | Tool       | When to use                                                  | Location                       |
| ------------ | ---------- | ------------------------------------------------------------ | ------------------------------ |
| Unit         | Vitest     | Pure logic — Zod schemas, JSON-RPC, no DB / no network        | `tests/unit/**`                |
| Integration  | Vitest     | Server actions, MCP tools, jobs against real Postgres         | `tests/integration/**`         |
| E2E          | Playwright | Critical user journeys — auth, OAuth connect, analytics load  | `tests/e2e/**`                 |

## Decision tree

\`\`\`
Server action / route handler / MCP tool? → Integration
Complex pure logic (no DB)?              → Unit
User-visible browser journey?             → One E2E happy-path
Trivial code / framework behaviour?       → Don't test
\`\`\`

## Coverage targets

- Server actions: ≥ 1 integration test per action
- MCP tools: ≥ 1 integration test per tool, including destructive ones (with `confirmed: true`)
- Public API routes: ≥ 1 happy-path + 1 auth-failure

## What must NEVER be untested

- Anything that mutates state (publish, delete, schedule)
- Anything that handles money (billing webhooks)
- Anything that handles auth (token issuance, revocation)

## Don't test

- Prisma / Drizzle relation casts
- Framework behaviour (Next.js routing, RSC rendering)
- Third-party SDKs (assume their tests cover them)
- Simple getters

## External HTTP

All external HTTP calls go through a service interface with a Null implementation for tests.
```

---

## docs/testing/fixtures.md

### Quiz

- Test data strategy (factories / seeders / snapshots)?
- Per-test isolation?

### Template

```markdown
# Test fixtures

## Strategy

<factories / seeders / snapshots> — link to the helper module.

## Per-test isolation

<how each test gets a clean DB — transaction rollback / truncate / per-test DB>
```

---

## docs/testing/mocking-services.md

### Quiz

- Service-interface pattern (Interface + Null impl)?
- List of mocked services?
- Where the Null impls live?

### Template

```markdown
# Mocking external services

## Pattern

Every external HTTP call goes through a service interface:

\`\`\`ts
export interface CrawlServiceInterface { crawl(url: string): Promise<string> }
export class HttpCrawlService implements CrawlServiceInterface { ... }
export class NullCrawlService implements CrawlServiceInterface { ... }
\`\`\`

The Null impl is used in tests; the Http impl is used in prod. Selection happens at the action boundary, never inside it.

## Service registry

| Service          | Interface path                                  | Null impl path                            |
| ---------------- | ----------------------------------------------- | ----------------------------------------- |
| <service>        | `src/lib/services/<service>/index.ts`           | `src/lib/services/<service>/null.ts`      |
```

---

## docs/contributing/pr-conventions.md

### Quiz

- Branch format?
- Commit format (Conventional Commits)?
- PR title format?
- Review SLA?
- Required reviewers?

### Template

```markdown
# PR conventions

## Branch format

`<type>/<short-kebab-description>` — e.g. `feat/add-billing-portal`, `fix/post-publish-500`.

## Commit format

Conventional Commits:

\`\`\`
<type>(<scope>): <description>

[optional body]

[optional footer]
\`\`\`

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`.

## PR title

Same as commit subject line.

## PR body

- **What** — one paragraph
- **Why** — link to issue / ADR
- **How to verify** — manual steps or `pnpm test:integration` output
- **Screenshots** — if UI change

## Review SLA

- First response: <24h business / <72h calendar>
- Approval: <2 approvals from CODEOWNERS>

## Required checks before merge

- `pnpm test`
- `pnpm test:integration` (if applicable)
- `pnpm test:e2e` (if UI changed)
- All conversations resolved
```

---

## docs/contributing/code-review-checklist.md

### Quiz

- Functions over 30 lines?
- Logic duplicated > 2×?
- Any `any` types?
- Missing error handling?
- `console.log` left in?

### Template

```markdown
# Code review checklist

## Complexity

- [ ] No function over 30 lines without justification
- [ ] No logic duplicated > 2×
- [ ] No new `any` types
- [ ] No `console.log` / `console.error` left in — use `logger` from `src/lib/logger.ts`

## Error handling

- [ ] All async ops wrapped with try/catch or explicit error handling
- [ ] Error messages use `getErrorMessage(err)` from `src/lib/utils.ts`
- [ ] No swallowed errors

## Testing

- [ ] New server action has an integration test
- [ ] New MCP tool has an integration test (including `confirmed: true` path if destructive)
- [ ] New UI page has a Playwright happy-path test

## Conventions

- [ ] Server components by default; `'use client'` only when needed
- [ ] TS types derived from Zod schemas (`z.infer<>`), not re-declared
- [ ] `next/image` for images, `next/link` for internal nav
- [ ] Env vars: `NEXT_PUBLIC_*` only for client-side
```

---

## docs/contributing/release-process.md

### Quiz

- Versioning (semver)?
- Changelog format?
- Release cadence?
- Deploy trigger (manual / auto on tag)?

### Template

```markdown
# Release process

## Versioning

<semver / calver / none>

## Changelog

<auto-generated from Conventional Commits / hand-written in CHANGELOG.md>

## Cadence

<weekly / per-PR / per-tag / continuous>

## Deploy trigger

<auto on tag / manual button / merge to main>
```

---

## docs/reference/glossary.md

### Quiz

- Domain terms (one-line defs each)?

### Template

```markdown
# Glossary

| Term                  | Definition                                                                                       |
| --------------------- | ------------------------------------------------------------------------------------------------ |
| <Term>                | <one-line definition, in the project's domain — not Wikipedia>                                   |
| ...                   | ...                                                                                              |
```

---

## docs/reference/env-vars.md

### Quiz

- For each var in `.env.example`: name, required/optional, where read, default, example?

### Template

```markdown
# Environment variables

> Source of truth for every env var the app reads. Update this whenever you add or remove a var.

## Required

| Name                       | Used in                  | Example                          | Notes           |
| -------------------------- | ------------------------ | -------------------------------- | --------------- |
| `DATABASE_URL`             | `src/lib/db/client.ts`   | `postgresql://…`                 |                 |
| ...                        | ...                      | ...                              | ...             |

## Optional

| Name                       | Default                  | Used in                          | Notes           |
| -------------------------- | ------------------------ | -------------------------------- | --------------- |
| `NEXT_PUBLIC_APP_URL`      | `http://localhost:3000`  | OAuth callbacks                 |                 |
| ...                        | ...                      | ...                              | ...             |

## Secrets vs public

Anything prefixed `NEXT_PUBLIC_` is bundled into the client. **Never** put secrets there.
```

---

## docs/reference/error-codes.md

### Quiz

- Stable error codes the agent can match on?
- Format (e.g. `AUTH_INVALID_KEY`)?

### Template

```markdown
# Error codes

> Stable, machine-readable codes agents can match on. Format: `<DOMAIN>_<ACTION>_<REASON>`.

| Code                            | HTTP | Meaning                                          | Resolution                              |
| ------------------------------- | ---- | ------------------------------------------------ | --------------------------------------- |
| `AUTH_INVALID_KEY`              | 401  | API key missing, malformed, or revoked           | User must rotate their key              |
| `AUTH_PROJECT_FORBIDDEN`        | 403  | Key valid but doesn't own the requested project  | User must grant access or switch keys   |
| `POST_PUBLISH_FAILED`           | 502  | Channel API rejected the post                    | Inspect `error.details`, retry or edit  |
| ...                             | ...  | ...                                              | ...                                     |

## Adding a new code

1. Pick a name that survives refactors (`POST_NOT_FOUND`, not `POST_404`).
2. Document it here.
3. Return it from the action with a stable message.
```

---

## docs/reference/events.md

### Quiz

- List of typed events?
- Payload shape per event?
- Listeners?

### Template

```markdown
# Events

> Typed event bus. Listeners subscribe to event names; payloads are validated with Zod.

## Events

| Event name              | Payload shape                              | Fired by          | Listened by         |
| ----------------------- | ------------------------------------------ | ----------------- | ------------------- |
| `post.published`        | `{ postId, channelId, publishedAt }`       | publish action    | audit, notification |
| `post.failed`           | `{ postId, channelId, error }`             | publish action    | audit, notification |
```

---

## docs/adr/NNN-<title>.md

### Quiz

- What's the decision?
- What alternatives were considered?
- What are the consequences?

### Template

See `references/adr-template.md` for the full Nygard template.

---

## docs/README.md

This file is auto-generated last — it's the index that links every other file.

### Quiz

- (No quiz needed — generated from the file tree.)

### Template

```markdown
# Documentation

> Agent entry point. Read this first, then follow links.

## Map

| I want to…                                | Read                                       |
| ----------------------------------------- | ------------------------------------------ |
| Understand what the product is            | `product/overview.md`                      |
| See what's shipped / planned              | `product/features.md`                      |
| Set up a new dev environment               | `deploy/environments.md` + `deploy/<platform>.md` |
| Add a server action / MCP tool            | `architecture/overview.md` + relevant ADR  |
| Change the data model                     | `architecture/data-model.md`               |
| Add a new env var                         | `reference/env-vars.md`                    |
| Look up a domain term                     | `reference/glossary.md`                    |
| Respond to a prod incident                | `operations/runbook.md`                    |
| Review a PR                               | `contributing/pr-conventions.md`           |

## Layout

\`\`\`
docs/
├── product/        # what we ship
├── architecture/   # how it's built
├── deploy/         # how it's deployed
├── operations/     # how it's run
├── security/       # how it's protected
├── business/       # how it's sold + supported
├── testing/        # how it's verified
├── contributing/   # how to contribute
├── reference/      # pure facts (glossary, env-vars, error-codes)
└── adr/            # irreversible decisions
\`\`\`

## Conventions

- Plain markdown — no frontmatter.
- ADRs are immutable. If the decision changed, write a new one.
- `AGENTS.md` is the index, not the content.
```