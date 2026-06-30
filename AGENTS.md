# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Repository Purpose

This is the source of truth for reusable AI agent **commands**, **skills**, and
**agents**. They are distributed to global install directories and per-project
directories via the `bin/sync` script.

This is not a runnable application — there are no build or test commands.

## Three-Layer Architecture

| Layer | Invoked by | Purpose | Format |
|-------|-----------|---------|--------|
| **Command** | Human only | Thin orchestrator that routes to skill(s) | Single `.md` in `commands/` |
| **Agent** | Command or human | Role-specific persona with perspective + output format | Single `.md` in `agents/` |
| **Skill** | Agent, command, or human | Reusable workflow with steps, constraints, exit criteria | Directory in `skills/` with `SKILL.md` + optional `references/` |

**Commands** are human-invoked workflows. The agent does not see them unless the
human explicitly invokes one. This keeps context lean.

**Agents** are role-specific personas (code-reviewer, security-auditor, test-engineer).
They evaluate and report — they do not orchestrate each other. Orchestration belongs
to commands.

**Skills** are designed for agent-initiated and/or human-initiated use. Their
`description` frontmatter is loaded into the agent's context on every run so the
agent knows when to activate them automatically.

## Directory Structure

```
repo root/
├── v2/
│   ├── commands/     # 9 slash commands (thin orchestrators)
│   ├── skills/       # 30 skill directories (reusable workflows)
│   ├── agents/       # 3 role-specific personas + README.md
│   └── AGENTS.md     # v2 authoring rules and conventions
├── bin/
│   └── sync          # distribution script (supports commands, skills, agents)
├── sync.example.toml # config template (tracked in git)
├── sync.toml         # local config (gitignored)
├── AGENTS.md         # this file
└── README.md         # catalog and usage
```

## Available Commands

9 commands in `v2/commands/`:

| Command | Description |
|---------|-------------|
| `build` | Implement tasks incrementally — build one thin slice at a time with TDD |
| `plan` | Break work into small, atomic, verifiable tasks; or produce a full PRD |
| `spec` | Start spec-driven development — write a structured specification before code |
| `review` | Two-lens PR review — code review then security audit |
| `ship` | Ship to production — pre-launch checklist, parallel persona fan-out, staged rollout |
| `test` | Run TDD workflow — write tests to prove behaviour |
| `simplify` | Simplify code — remove unnecessary complexity while preserving behaviour |
| `learn` | Extract non-obvious lessons from the current session |
| `triage` | Triage GitHub issues via an autonomous label-based state machine |

## Available Agents

3 agents in `v2/agents/`:

| Agent | Description |
|-------|-------------|
| `code-reviewer` | Five-axis code review (correctness, readability, architecture, security, performance) |
| `security-auditor` | Threat-model-focused security review (OWASP, STRIDE, LLM Top 10) |
| `test-engineer` | Test coverage analysis, test design, Prove-It pattern for bugs |

## Available Skills

30 skills in `v2/skills/`:

| Skill | Description |
|-------|-------------|
| `architecture-review` | Surface architectural friction as GitHub issues |
| `browser-testing-with-devtools` | Debug browser UI via chrome-devtools MCP |
| `code-review-and-quality` | Five-axis PR review before merge |
| `code-simplification` | Remove unnecessary complexity in the codebase |
| `content-editor` | Autonomous content pipeline: queue, ingest, write, quality gates, publish |
| `context-engineering` | Discover and hierarchically load project context |
| `debugging-and-error-recovery` | Systematic root-cause debugging |
| `deprecation-and-migration` | Safely deprecate systems and migrate users |
| `documentation-and-adrs` | Write ADRs, comment the why, maintain docs |
| `doubt-driven-development` | Adversarial self-review for high-stakes changes |
| `frontend-ui-engineering` | Production-quality UI: components, state, accessibility |
| `git-workflow-and-versioning` | Trunk-based development, atomic commits, worktrees |
| `incremental-implementation` | Implement planned tasks slice-by-slice with TDD |
| `interview-me` | One-question-at-a-time interviewing to clarify underspecified asks |
| `learn-from-session` | Extract non-obvious lessons to AGENTS.md or skill files |
| `observability-and-instrumentation` | Structured logs, RED metrics, alert on symptoms |
| `performance-optimimization` | Measure-first performance optimization |
| `planning-and-task-breakdown` | Decompose specs into thin vertical slices |
| `quality-gate` | Run lint + typecheck + test + build, stop-the-line on failure |
| `security-and-hardening` | Threat-model-first security controls |
| `shipping-and-launch` | Pre-launch checklist, staged rollout, rollback readiness |
| `source-driven-development` | Fetch and cite official docs instead of hallucinating |
| `spec-driven-development` | Write a structured specification before writing code |
| `standards-api` | RESTful API design conventions and best practices |
| `standards-laravel` | Laravel backend conventions |
| `standards-nextjs` | Next.js 15 App Router project conventions |
| `standards-php` | PHP 8.3+ best practices: DTOs, Value Objects, exceptions |
| `standards-typescript` | TypeScript project conventions |
| `test-driven-development` | RED → GREEN → REFACTOR TDD workflow |
| `writing-humanizer` | Remove signs of AI-generated writing from text |

## Distribution: bin/sync

The `sync.toml` config (gitignored — copy from `sync.example.toml`) defines:

- **source_dir** — source directory (set to `v2`)
- **Global targets** — directories for global command/skill/agent symlinks
- **Global install lists** — which commands/skills/agents to install globally
- **Projects** — per-project paths and their desired commands/skills/agents

Target directories (per opencode's discovery rules):
- **Commands (global):** `~/.config/opencode/commands/`
- **Commands (per-project):** `<project>/.opencode/commands/`
- **Skills (global):** `~/.config/opencode/skills/` and `~/.agents/skills/`
- **Skills (per-project):** `<project>/.agents/skills/`
- **Agents (global):** `~/.config/opencode/agents/`
- **Agents (per-project):** `<project>/.opencode/agents/`

Standards skills (`standards-*`) are project-specific and installed per-project.
General-purpose skills are installed globally.

Usage:

```
bin/sync              # install/update all symlinks
bin/sync --dry-run    # preview changes only
bin/sync --clean      # remove stale symlinks, skip install
```