# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Repository Purpose

This is the source of truth for reusable AI agent **commands** and **skills**.
They are distributed to global install directories and per-project directories
via the `bin/sync` script.

This is not a runnable application — there are no build or test commands.

## Commands vs Skills

| Attribute | Commands | Skills |
|-----------|----------|--------|
| Invoked by | Human only | Agent and/or human |
| Loaded in context | No — loaded on demand | Yes — description preloaded for auto-activation |
| Format | Single `.md` file in `commands/` | Directory under `skills/` with `SKILL.md` + optional `references/` |
| Frontmatter | `description:`, optional `global:` | `name:`, `description:` (starts with "Use when...") |

**Commands** are human-invoked workflows. The agent does not see them unless the
human explicitly invokes one. This keeps context lean and avoids polluting the
agent's context with seldom-used instructions.

**Skills** are designed for agent-initiated and/or human-initiated use. Their
`description` frontmatter is loaded into the agent's context on every run so
the agent knows when to activate them automatically.

## Directory Structure

```
repo root/
├── commands/               # flat .md files (human-invoked)
│   ├── dev-implement.md
│   ├── plan-bug.md
│   └── ...
├── skills/                 # directories with SKILL.md
│   ├── standards-api/
│   │   ├── SKILL.md
│   │   └── references/
│   └── ...
├── bin/
│   └── sync                # distribution script
├── sync.example.toml       # config template (tracked in git)
├── sync.toml               # local config (gitignored)
├── AGENTS.md
└── README.md
```

## Available Commands

11 commands are available in `commands/`. See `README.md` for the full catalog
grouped by domain. Each command has a `description` in its frontmatter.

## Available Skills

11 skills are available in `skills/`:

| Skill | Description |
|-------|-------------|
| `content-editor` | Autonomous content pipeline: queue, ingest, write, quality gates, publish |
| `dev-simplify` | Identify and remove unnecessary complexity in the codebase |
| `ops-architecture-review` | Surface architectural friction as GitHub issues |
| `pr-review` | Automated PR review producing a `## Automated review` comment, applying risk label and `reviewed` on completion |
| `quality-gate` | Run lint + typecheck + test + build, stop-the-line on failure |
| `standards-api` | RESTful API design conventions and best practices |
| `standards-laravel` | Laravel backend conventions: models, controllers, actions, resources, migrations |
| `standards-nextjs` | Next.js 15 App Router project conventions |
| `standards-php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |
| `standards-typescript` | TypeScript project conventions |
| `writing-humanizer` | Remove signs of AI-generated writing from text |

## Distribution: bin/sync

The `sync.toml` config (gitignored — copy from `sync.example.toml`) defines:

- **Global targets** — directories for global command/skill symlinks
- **Global install list** — which commands/skills to install globally
- **Projects** — per-project paths and their desired commands/skills

Usage:

```
bin/sync              # install/update all symlinks
bin/sync --dry-run    # preview changes only
bin/sync --clean      # remove stale symlinks, skip install
```

## Skill Authoring Rules

**Frontmatter** — Skills require `name` and `description` only. The `description`
must start with "Use when..." and describe trigger conditions.

**Body — include:**
- Core Workflow — ordered steps
- Reference Guide — table of reference files with load conditions (only if reference files exist)
- Constraints — MUST DO / MUST NOT DO behavioural rules

**Body — do not include:**
- Role Definition (persona prompting adds no value with modern models)
- "When To Use This Skill" section (belongs in frontmatter description only)
- Empty Reference Guide tables
- Intro blurbs that restate the skill name
