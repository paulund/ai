# claude-skills

Personal Claude Code global skills for Laravel, Inertia.js, and related development workflows.

## Skills

### Laravel

Skills for building Laravel applications following consistent conventions. Covers the full backend lifecycle — from structuring controllers, models, and Eloquent relationships, to actions, jobs, events, policies, and API resources. The testing skill adds Pest-based coverage with factories, feature tests, and Inertia assertions. The Sail skill handles the Docker development environment.

| Skill | Description |
|-------|-------------|
| `laravel` | Controllers, models, Eloquent, actions, enums, jobs, events, policies, API resources, migrations |
| `laravel-testing` | Testing with Pest: factories, unit tests, Inertia testing |
| `laravel-sail` | Sail commands, docker-compose, service configuration, troubleshooting |
| `inertia` | Inertia page props, shared data, forms, navigation, partial reloads, deferred props |

### PHP

Best practices for modern PHP development independent of any framework. Enforces strict types, immutable Value Objects, typed DTOs, and structured exception hierarchies following PSR standards.

| Skill | Description |
|-------|-------------|
| `php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |

### API Design

Conventions for designing consistent, well-documented REST APIs. Covers resource naming, HTTP methods, status codes, error shapes, pagination, filtering, authentication, versioning, and OpenAPI specification.

| Skill | Description |
|-------|-------------|
| `api` | RESTful API design conventions, HTTP methods, status codes, and best practices |
| `webhook` | Webhook implementation with Standard Webhooks conventions |

### Git & CI/CD

Skills for consistent Git workflows, automated releases, and GitHub Actions pipelines. Handles branching strategy, commit conventions, PR creation, changelog generation, and version tagging.

| Skill | Description |
|-------|-------------|
| `dev-commit-push-pr` | Git commit, push, and PR workflow |
| `release-github-release` | GitHub release automation |
| `git-github-actions-claude` | GitHub Actions with Claude integration |
| `merge-main` | Merge origin/main into current branch and resolve conflicts |
| `bump-version` | Version bumping automation |

### TypeScript & Next.js

Best practices for TypeScript strict-mode development and Next.js 15 App Router patterns. Covers type safety, Zod integration, Prisma types, server vs client components, server actions, data fetching, caching, and streaming.

| Skill | Description |
|-------|-------------|
| `typescript-best-practices` | TypeScript strict-mode: types, Zod, Prisma, React typing conventions |
| `nextjs-best-practices` | Next.js 15 App Router: server/client components, server actions, caching, routing |

### Infrastructure

Skills for containerisation, observability, and security. Covers Docker Compose service wiring, structured logging, metrics, alerting, and security auditing across frontend, backend, and infrastructure layers.

| Skill | Description |
|-------|-------------|
| `docker` | Docker Compose healthchecks, service dependencies, container readiness |
| `monitoring` | Observability, metrics, structured logging, and alerting patterns |
| `security` | Security auditing, vulnerability scanning, and secure coding practices |

### Dev Loop

Skills for autonomous feature development. `plan` turns an idea into dependency-ordered GitHub issues; `ship` picks the next ready issue, implements it with TDD, runs the quality gate, and opens a reviewed PR — designed to run repeatedly via `/loop /ship`.

| Skill | Description |
|-------|-------------|
| `plan` | Turn an idea into vertical-sliced GitHub issues ready for the ship loop |
| `ship` | Pick the next AFK issue, implement with TDD, run quality gate, open PR |

### Code Quality

Review and refactoring skills that apply consistent standards across backend and frontend code. The `learnt` skill extracts lessons from the current session and saves them to the right place — memories, CLAUDE.md, or global skill files.

| Skill | Description |
|-------|-------------|
| `code-review` | Code review checklists for backend and frontend |
| `learnt` | Extract session lessons and save them to the right place |

### WordPress

Skills for building WordPress themes and plugins to modern standards. Covers plugin architecture, theme development, Gutenberg block creation, hooks and filters, and performance and security hardening.

| Skill | Description |
|-------|-------------|
| `wordpress` | WordPress theme and plugin development, Gutenberg blocks, hooks and filters |

### Marketing

Skills for creating and distributing content across channels. Covers platform-specific writing for X and LinkedIn, copywriting frameworks, email sequences, SEO auditing, and repurposing long-form content into social posts.

| Skill | Description |
|-------|-------------|
| `marketing-content-repurposer` | Repurpose content across platforms |
| `marketing-content-strategy` | Content strategy planning |
| `marketing-copywriting` | Copywriting frameworks and guides |
| `marketing-email-sequence` | Email sequence writing |
| `marketing-linkedin-content` | LinkedIn post creation |
| `marketing-reply-drafter` | Reply drafting for social media |
| `marketing-seo-audit` | SEO auditing checklist |
| `marketing-x-content` | X (Twitter) post creation |

### SaaS

Skills for planning and launching SaaS products. Covers writing product requirements documents, building go-to-market plans, pricing strategy, and validating launch readiness against a structured checklist.

| Skill | Description |
|-------|-------------|
| `saas-go-to-market` | Go-to-market strategy |
| `saas-launch-checklist` | Launch readiness checklist |
| `saas-pricing-strategy` | Pricing strategy frameworks |
| `saas-prd` | SaaS PRD framework |

### Writing

Skills for improving written output. The humanizer removes AI-sounding patterns from generated text; the technical writer skill applies consistent style, structure, and clarity to documentation and guides.

| Skill | Description |
|-------|-------------|
| `humanizer` | Humanize AI-generated text |
| `writing-technical-writer` | Technical writing style and structure |

## Installation

Clone this repo and copy any skills you want into `~/.claude/skills/`:

```bash
git clone https://github.com/paulund/ai.git
cd ai

# Install individual skills
cp -r laravel ~/.claude/skills/
cp -r dev-commit-push-pr ~/.claude/skills/

# Or install all skills
for d in */; do cp -r "$d" ~/.claude/skills/; done
```

## Usage

Skills are invoked by name in Claude Code:

```
/laravel
/dev-commit-push-pr
/release-github-release
```
