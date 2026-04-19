# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Repository Purpose

This is a collection of reusable Claude Code skills, intended to be installed as global skills in `~/.claude/skills/`. It is not a runnable application; there are no build or test commands.

## Structure

All skills live at the repo root. Skills from multi-skill groups are prefixed with the group name:

```
repo root/
├── api/
├── code-review/
├── docker/
├── git-commit-push-pr/
├── git-github-release/
├── laravel/
├── laravel-testing/
├── marketing-copywriting/
├── marketing-content-strategy/
└── ...
```

Each skill directory contains a `SKILL.md` and an optional `references/` subdirectory.

### Skills

- **`api`** — RESTful API design conventions and best practices
- **`code-review`** — Code review checklists for backend and frontend
- **`docker`** — Docker Compose healthchecks, service dependencies, container readiness
- **`git-commit-push-pr`** — Git commit, push, and PR workflow
- **`git-github-actions-claude`** — GitHub Actions with Claude integration
- **`git-github-release`** — GitHub release automation
- **`inertia`** — Inertia.js page props, forms, shared data, navigation
- **`laravel`** — Laravel backend conventions: models, controllers, actions, resources, migrations
- **`laravel-testing`** — Laravel testing: factories, unit tests, Inertia testing
- **`laravel-sail`** — Laravel Sail Docker commands and service management
- **`marketing-content-repurposer`** — Repurpose content across platforms
- **`marketing-content-strategy`** — Content strategy planning
- **`marketing-copywriting`** — Copywriting frameworks and guides
- **`marketing-email-sequence`** — Email sequence writing
- **`marketing-linkedin-content`** — LinkedIn post creation
- **`marketing-reply-drafter`** — Reply drafting for social media
- **`marketing-seo-audit`** — SEO auditing checklist
- **`marketing-x-content`** — X (Twitter) post creation
- **`monitoring`** — Observability, metrics, structured logging, alerting
- **`php`** — PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards
- **`saas-go-to-market`** — Go-to-market strategy
- **`saas-launch-checklist`** — Launch readiness checklist
- **`saas-pricing-strategy`** — Pricing strategy frameworks
- **`saas-prd`** — SaaS PRD framework
- **`security`** — Security auditing and secure coding practices
- **`webhook`** — Webhook implementation with Standard Webhooks
- **`wordpress`** — WordPress theme/plugin development, Gutenberg, hooks
- **`writing-humanizer`** — Humanize AI-generated text
- **`writing-technical-writer`** — Technical writing style and structure
- **`bump-version`** — Version bumping automation

### Installing as Global Skills

Copy the skills you want to `~/.claude/skills/`:

```bash
cp -r laravel ~/.claude/skills/
cp -r git-commit-push-pr ~/.claude/skills/
# or copy all (excluding CLAUDE.md and README.md)
for d in */; do cp -r "$d" ~/.claude/skills/; done
```

## Skill Authoring Rules

**Frontmatter** — Skills require `name` and `description` only. The `description` must start with "Use when..." and describe trigger conditions.

**Body — include:**
- Core Workflow — ordered steps
- Reference Guide — table of reference files with load conditions (only if reference files exist)
- Constraints — MUST DO / MUST NOT DO behavioural rules

**Body — do not include:**
- Role Definition (persona prompting adds no value with modern models)
- "When To Use This Skill" section (belongs in frontmatter description only)
- Empty Reference Guide tables
- Intro blurbs that restate the skill name
