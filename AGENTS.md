# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Repository Purpose

This is the source of truth for reusable AI agent **prompts** and **skills**.
They are distributed to global install directories and per-project directories
via the `bin/sync` script.

This is not a runnable application — there are no build or test commands.

## Prompts vs Skills

| Attribute | Prompts | Skills |
|-----------|---------|--------|
| Invoked by | Human only | Agent and/or human |
| Loaded in context | No — loaded on demand | Yes — description preloaded for auto-activation |
| Format | Single `.md` file in `prompts/` | Directory under `skills/` with `SKILL.md` + optional `references/` |
| Frontmatter | `description:`, optional `global:` | `name:`, `description:` (starts with "Use when...") |

**Prompts** are human-invoked workflows. The agent does not see them unless the
human explicitly invokes one. This keeps context lean and avoids polluting the
agent's context with seldom-used instructions.

**Skills** are designed for agent-initiated and/or human-initiated use. Their
`description` frontmatter is loaded into the agent's context on every run so
the agent knows when to activate them automatically.

## Directory Structure

```
repo root/
├── prompts/                # flat .md files (human-invoked)
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

## Available Prompts

33 prompts are available in `prompts/`. See `README.md` for the full catalog
grouped by domain. Each prompt has a `description` in its frontmatter and may
set `global: true` to indicate it's intended for global install.

## Available Skills

6 skills are available in `skills/`:

| Skill | Description |
|-------|-------------|
| `standards-api` | RESTful API design conventions and best practices |
| `standards-laravel` | Laravel backend conventions: models, controllers, actions, resources, migrations |
| `standards-nextjs` | Next.js 15 App Router project conventions |
| `standards-php` | PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards |
| `standards-typescript` | TypeScript project conventions |
| `writing-humanizer` | Remove signs of AI-generated writing from text |

## Distribution: bin/sync

The `sync.toml` config (gitignored — copy from `sync.example.toml`) defines:

- **Global targets** — directories for global prompt/skill symlinks
- **Global install list** — which prompts/skills to install globally
- **Projects** — per-project paths and their desired prompts/skills

Usage:

```
bin/sync              # install/update all symlinks
bin/sync --dry-run    # preview changes only
bin/sync --clean      # remove stale symlinks, skip install
```

### Config format

```toml
[global]
prompt_targets = ["~/.claude/prompts/"]
skill_targets  = ["~/.claude/skills/", "~/.agents/skills/"]

prompts = ["dev-implement", "plan-bug", ...]
skills  = ["standards-api", "standards-laravel", ...]

[[project]]
path    = "~/Documents/Code/my-app"
prompts = ["dev-implement"]
skills  = ["standards-laravel"]
```

Global prompts become file symlinks in each `prompt_target`.
Global skills become directory symlinks in each `skill_target`.
Per-project installs go to `<path>/.agents/prompts/` and `<path>/.agents/skills/`.

The script auto-cleans stale symlinks — any symlink pointing to this repo
that is no longer in the config is removed.

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
