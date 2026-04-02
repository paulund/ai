# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Repository Purpose

This is a Claude Code **plugin marketplace** — a collection of reusable skill plugins distributed via the `/plugin` command. It is not a runnable application; there are no build or test commands.

## Structure

The repo root contains a `.claude-plugin/marketplace.json` that catalogues all plugins. Each plugin is a top-level directory with its own `.claude-plugin/plugin.json` manifest and a `skills/` subdirectory:

```
repo root/
├── .claude-plugin/
│   └── marketplace.json       # marketplace catalogue
├── laravel/
│   ├── .claude-plugin/
│   │   └── plugin.json        # plugin manifest
│   └── skills/
│       └── laravel/
│           ├── SKILL.md
│           └── references/    # optional
└── ...
```

### Plugins

- **`laravel`** — Laravel backend conventions: models, controllers, actions, resources, migrations, testing
- **`inertia`** — Inertia.js page props, forms, shared data, navigation
- **`react`** — React + TypeScript components, hooks, state management
- **`laravel-sail`** — Laravel Sail Docker commands and service management
- **`api`** — RESTful API design conventions and best practices
- **`code-review`** — Code review checklists for backend and frontend
- **`git`** — Git workflow: commit-push-PR, GitHub releases
- **`monitoring`** — Observability, metrics, structured logging, alerting
- **`security`** — Security auditing and secure coding practices
- **`webhook`** — Webhook implementation with Standard Webhooks
- **`php`** — PHP 8.3+ best practices: strict types, DTOs, Value Objects, exceptions, PSR standards
- **`wordpress`** — WordPress theme/plugin development, Gutenberg, hooks
- **`docker`** — Docker Compose healthchecks, service dependencies, container readiness

### Usage

Add the marketplace and install plugins:

```
/plugin marketplace add paulund/ai
/plugin install laravel@paulund-ai
```

Or for local development, use `--plugin-dir`:

```bash
claude --plugin-dir ./laravel
```

Skills are namespaced by plugin name (e.g. `/laravel:laravel`, `/git:commit-push-pr`).

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
