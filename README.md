# AI Prompts & Skills

Source of truth for reusable AI agent prompts and skills.
Distributed via `bin/sync` to global and per-project directories.

## Prompts vs Skills

- **Prompts** — human-invoked workflows. Loaded on demand, not in context.
  Single `.md` files in `prompts/`.
- **Skills** — agent and/or human invoked. Description preloaded in context for
  auto-activation. Directories under `skills/` with `SKILL.md` + optional `references/`.

## Quick Start

```bash
# 1. Create your local config (gitignored)
cp sync.example.toml sync.toml

# 2. Edit sync.toml to match your setup
#    - Set global target directories
#    - List the prompts/skills you want globally
#    - Define any per-project installs

# 3. Run the sync script
bin/sync              # install/update
bin/sync --dry-run    # preview
```

## Available Prompts

32 prompts in `prompts/`, grouped by domain.

### Dev Loop

| Prompt | Description |
|--------|-------------|
| `dev-implement` | Implement a single AFK GitHub issue end-to-end with TDD |
| `dev-merge-main` | Merge origin/main into the current branch |
| `quality-gate` | Run lint + types + tests + build as a pre-push gate |
| `pr-review` | Two-phase code + security review with findings as commits |
| `pr-fix` | Action external review feedback and CI failures |
| `pr-verify` | Boot dev server and drive UI via DevTools for screenshot verification |

### Planning

| Prompt | Description |
|--------|-------------|
| `plan-bug` | Interview user about a bug, file as planned+AFK issue |
| `plan-grill-me` | Relentless interview until reaching shared understanding |
| `plan-to-prd` | Turn idea into PRD + vertical-sliced GitHub issues |

### Ops

| Prompt | Description |
|--------|-------------|
| `ops-triage` | GitHub issue state machine — triage, brief, close, block |
| `ops-architecture-review` | Surface architectural friction as GitHub issues |
| `ops-vercel-log-scan` | Scan Vercel runtime logs, triage errors to root cause |

### Learning

| Prompt | Description |
|--------|-------------|
| `learn` | Extract session lessons and save to AGENTS.md or skill files |
| `learn-prs` | Extract lessons from PR review comments over last 7 days |

### Release

| Prompt | Description |
|--------|-------------|
| `release-github-release` | Determine semver, write release notes, create tag |

### Content Operations

This is now a **skill** — see [skills/content-editor/](/skills/content-editor/).

### Writing

| Prompt | Description |
|--------|-------------|
| `writing-content-draft` | Write a full publishable article from sources |
| `writing-content-evergreen` | Refresh stale Next.js/TypeScript articles |
| `writing-content-pr-to-blog` | Turn merged PR into blog post |
| `writing-github-release-articles` | Write technical article from GitHub release |
| `writing-newsletter-draft` | Generate weekly newsletter from recent content |
| `writing-seo-reviewer` | SEO audit for paulund.co.uk content |

### Personal

| Prompt | Description |
|--------|-------------|
| `todo` | Morning project management checkpoint across repos |
| `url-manager` | URL structure changes for paulund.co.uk |


## Available Skills

7 skills in `skills/`:

| Skill | Description |
|-------|-------------|
| `dev-simplify` | Identify and remove unnecessary complexity in the codebase |
| `standards-api` | RESTful API design conventions and best practices |
| `standards-laravel` | Laravel backend conventions: models, controllers, actions, resources, migrations |
| `standards-nextjs` | Next.js 15 App Router project conventions |
| `standards-php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |
| `standards-typescript` | TypeScript project conventions |
| `writing-humanizer` | Remove signs of AI-generated writing from text |

## Adding a New Prompt

Create `prompts/<name>.md` with frontmatter:

```yaml
---
description: What this prompt does, when to use it.
---
```

## Adding a New Skill

Create `skills/<name>/SKILL.md` with frontmatter. See the Skill Authoring Rules
in `AGENTS.md`.

## Configuration Reference

See `sync.example.toml` for the full config format with inline documentation.
