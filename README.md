# AI Commands & Skills

Source of truth for reusable AI agent commands and skills.
Distributed via `bin/sync` to global and per-project directories.

## Commands vs Skills

- **Commands** — human-invoked workflows. Loaded on demand, not in context.
  Single `.md` files in `commands/`.
- **Skills** — agent and/or human invoked. Description preloaded in context for
  auto-activation. Directories under `skills/` with `SKILL.md` + optional `references/`.

## Quick Start

```bash
# 1. Create your local config (gitignored)
cp sync.example.toml sync.toml

# 2. Edit sync.toml to match your setup
#    - Set global target directories
#    - List the commands/skills you want globally
#    - Define any per-project installs

# 3. Run the sync script
bin/sync              # install/update
bin/sync --dry-run    # preview
```

## Available Commands

11 commands in `commands/`, grouped by domain.

### Dev Loop

| Command | Description |
|---------|-------------|
| `dev-implement` | Implement a single AFK GitHub issue end-to-end with TDD |
| `dev-merge-main` | Merge origin/main into the current branch |
| `pr-verify` | Boot dev server and drive UI via DevTools for screenshot verification |

### Planning

| Command | Description |
|---------|-------------|
| `plan-bug` | Interview user about a bug, file as planned+AFK issue |
| `plan-grill-me` | Relentless interview until reaching shared understanding |
| `plan-to-prd` | Turn idea into PRD + vertical-sliced GitHub issues, gated by a planning session (UX per surface) and a per-slice priority rubric |

### Ops

| Command | Description |
|---------|-------------|
| `ops-triage` | GitHub issue state machine — triage, brief, close, block |
| `ops-vercel-log-scan` | Scan Vercel runtime logs, triage errors to root cause |

### Learning

| Command | Description |
|---------|-------------|
| `learn` | Extract session lessons and save to AGENTS.md or skill files |
| `learn-prs` | Extract lessons from PR review comments over last 7 days |

### Release

| Command | Description |
|---------|-------------|
| `github-release` | Determine semver, write release notes, create tag |

### Content Operations

This is now a **skill** — see [skills/content-editor/](/skills/content-editor/).

## Available Skills

12 skills in `skills/`:

| Skill | Description |
|-------|-------------|
| `content-editor` | Autonomous content pipeline: queue scan, source ingestion, readiness evaluation, writing via style guide, quality gates, PR publishing |
| `dev-simplify` | Identify and remove unnecessary complexity in the codebase |
| `ops-architecture-review` | Identify architectural friction, produce GitHub issues with ops-triage labels |
| `quality-gate` | Run lint → typecheck → test → build, stop on first failure |
| `pr-review` | Two-phase code + security review with findings as commits |
| `git-worktree` | Create or delete isolated git worktrees for clean agent sessions |
| `standards-api` | RESTful API design conventions and best practices |
| `standards-laravel` | Laravel backend conventions: models, controllers, actions, resources, migrations |
| `standards-nextjs` | Next.js 15 App Router project conventions |
| `standards-php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |
| `standards-typescript` | TypeScript project conventions |
| `writing-humanizer` | Remove signs of AI-generated writing from text |

## Adding a New Command

Create `commands/<name>.md` with frontmatter:

```yaml
---
description: What this command does, when to use it.
---
```

## Adding a New Skill

Create `skills/<name>/SKILL.md` with frontmatter. See the Skill Authoring Rules
in `AGENTS.md`.

## Configuration Reference

See `sync.example.toml` for the full config format with inline documentation.
