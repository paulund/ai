# claude-skills

Personal Claude Code global skills for Laravel, Inertia.js, and related development workflows.

## Skills

| Skill | Description |
|-------|-------------|
| `laravel` | Controllers, models, Eloquent, actions, enums, jobs, events, policies, API resources, migrations |
| `laravel-testing` | Testing with Pest: factories, unit tests, Inertia testing |
| `laravel-sail` | Sail commands, docker-compose, service configuration, troubleshooting |
| `inertia` | Inertia page props, shared data, forms, navigation, partial reloads, deferred props |
| `api` | RESTful API design conventions, HTTP methods, status codes, and best practices |
| `code-review` | Code review checklists for backend and frontend |
| `docker` | Docker Compose healthchecks, service dependencies, container readiness |
| `git-commit-push-pr` | Git commit, push, and PR workflow |
| `git-github-release` | GitHub release automation |
| `git-github-actions-claude` | GitHub Actions with Claude integration |
| `monitoring` | Observability, metrics, structured logging, and alerting patterns |
| `security` | Security auditing, vulnerability scanning, and secure coding practices |
| `webhook` | Webhook implementation with Standard Webhooks conventions |
| `php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |
| `wordpress` | WordPress theme and plugin development, Gutenberg blocks, hooks and filters |
| `marketing-content-repurposer` | Repurpose content across platforms |
| `marketing-content-strategy` | Content strategy planning |
| `marketing-copywriting` | Copywriting frameworks and guides |
| `marketing-email-sequence` | Email sequence writing |
| `marketing-linkedin-content` | LinkedIn post creation |
| `marketing-reply-drafter` | Reply drafting for social media |
| `marketing-seo-audit` | SEO auditing checklist |
| `marketing-x-content` | X (Twitter) post creation |
| `saas-go-to-market` | Go-to-market strategy |
| `saas-launch-checklist` | Launch readiness checklist |
| `saas-pricing-strategy` | Pricing strategy frameworks |
| `saas-prd` | SaaS PRD framework |
| `writing-humanizer` | Humanize AI-generated text |
| `writing-technical-writer` | Technical writing style and structure |
| `bump-version` | Version bumping automation |

## Installation

Clone this repo and copy any skills you want into `~/.claude/skills/`:

```bash
git clone https://github.com/paulund/ai.git

# Install individual skills
cp -r ai/laravel ~/.claude/skills/
cp -r ai/git-commit-push-pr ~/.claude/skills/

# Or install all skills
for d in ai/*/; do cp -r "$d" ~/.claude/skills/; done
```

## Usage

Skills are invoked by name in Claude Code:

```
/laravel
/git-commit-push-pr
/git-github-release
```
