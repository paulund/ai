# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Repository Purpose

This is a collection of reusable AI agent skills, intended to be installed as global skills in `~/.claude/skills/` or your agent's equivalent skills directory. It is not a runnable application; there are no build or test commands.

## Structure

All skills live at the repo root. Skills from multi-skill groups are prefixed with the group name:

```
repo root/
├── standards-api/
├── review/
├── standards-docker/
├── dev-commit-push-pr/
├── release-github-release/
├── standards-laravel/
├── marketing-copywriting/
├── marketing-content-strategy/
└── ...
```

Each skill directory contains a `SKILL.md` and an optional `references/` subdirectory.

### Skills

### Dev loop (chain skills)

These skills compose into the AFK ship loop run by the agent-orchestration scheduler. Each step is its own context — no mega-skills, no sub-agent fan-out.

- **`dev-implement`** — Implement one issue with TDD on a pre-prepared branch (chain step 1)
- **`quality-gate`** — Lint + types + tests + build with Stop-the-Line failure handling (chain step, reusable between phases)
- **`dev-simplify`** — Cleanup pass over recently changed code (chain step)
- **`pr-open`** — Open a PR for a pushed branch and transition issue labels (chain step)
- **`pr-review`** — Read PR diff in fresh context, action review findings, commit (chain step)
- **`pr-security-review`** — Same shape, security focus (chain step)
- **`pr-verify`** — Boot dev server, drive UI via Chrome DevTools MCP, post screenshot summary (chain step + label-driven)
- **`pr-fix`** — Action external review feedback and CI failures on an existing PR (label-driven)
- **`dev-merge-main`** — Merge `origin/main` into the current branch and resolve conflicts (label-driven on `pr-afk` + DIRTY)
- **`dev-commit-push-pr`** — Git commit, push, and PR workflow (ad-hoc human use)
- **`dev-ship`** *(deprecated — being replaced by the chain above; remove after orchestrator cutover)*
- **`plan-bug`** — Interview the user about a bug and file it as a planned+afk GitHub issue
- **`plan-to-prd`** — Turn an idea into vertical-sliced GitHub issues for the ship loop
- **`bump-version`** — Version bumping automation
- **`review`** — Code review checklists for backend and frontend
- **`standards-api`** — RESTful API design conventions and best practices
- **`standards-docker`** — Docker Compose healthchecks, service dependencies, container readiness
- **`standards-inertia`** — Inertia.js page props, forms, shared data, navigation
- **`standards-laravel`** — Laravel backend conventions: models, controllers, actions, resources, migrations
- **`standards-php`** — PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards
- **`standards-pre-commit`** — Pre-commit hooks with Husky, lint-staged, and Prettier
- **`standards-tdd`** — Test-driven development: red-green-refactor loop, interface design, mocking boundaries
- **`standards-webhook`** — Webhook implementation with Standard Webhooks
- **`standards-wordpress`** — WordPress theme and plugin development, Gutenberg, hooks
- **`git-github-actions-claude`** — GitHub Actions with Claude integration
- **`release-github-release`** — GitHub release automation
- **`laravel-sail`** — Laravel Sail Docker commands and service management
- **`laravel-testing`** — Laravel testing: factories, unit tests, Inertia testing
- **`marketing-content-repurposer`** — Repurpose content across platforms
- **`marketing-content-strategy`** — Content strategy planning
- **`marketing-copywriting`** — Copywriting frameworks and guides
- **`marketing-email-sequence`** — Email sequence writing
- **`marketing-linkedin-content`** — LinkedIn post creation
- **`marketing-reply-drafter`** — Reply drafting for social media
- **`marketing-seo-audit`** — SEO auditing checklist
- **`marketing-x-content`** — X (Twitter) post creation
- **`ops-monitoring`** — Observability, metrics, structured logging, alerting
- **`ops-triage`** — Triage GitHub issues through a label-based state machine
- **`ops-backlog-health`** — Periodic non-interactive scrub: close resolved blockers, surface drift
- **`saas-go-to-market`** — Go-to-market strategy
- **`saas-launch-checklist`** — Launch readiness checklist
- **`saas-pricing-strategy`** — Pricing strategy frameworks
- **`saas-prd`** — SaaS PRD framework
- **`security`** — Security auditing and secure coding practices
- **`writing-humanizer`** — Humanize AI-generated text
- **`writing-technical-writer`** — Technical writing style and structure

### Installing as Global Skills

Copy the skills you want to `~/.claude/skills/`:

```bash
cp -r standards-laravel ~/.claude/skills/
cp -r dev-commit-push-pr ~/.claude/skills/
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
