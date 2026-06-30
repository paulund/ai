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
├── commands/     # slash commands (thin orchestrators)
├── skills/       # skill directories (reusable workflows)
├── agents/       # role-specific personas + README.md
├── bin/
│   └── sync      # distribution script (supports commands, skills, agents)
├── sync.example.toml # config template (tracked in git)
├── sync.toml         # local config (gitignored)
├── AGENTS.md         # this file
└── README.md         # catalog and usage
```

## Available Commands

Commands in `commands/`:

| Command | Description |
|---------|-------------|
| `build` | Implement tasks incrementally — build one thin slice at a time with TDD |
| `learn` | Extract non-obvious lessons from the current session |
| `plan` | Break work into small, atomic, verifiable tasks; or produce a full PRD |
| `resolve-conflicts` | Merge origin/main into the current branch and resolve all conflicts |
| `review` | Two-lens PR review — code review then security audit |
| `ship` | Ship to production — pre-launch checklist, parallel persona fan-out, staged rollout |
| `simplify` | Simplify code — remove unnecessary complexity while preserving behaviour |
| `spec` | Start spec-driven development — write a structured specification before code |
| `test` | Run TDD workflow — write tests to prove behaviour |
| `triage` | Triage GitHub issues via an autonomous label-based state machine |
| `verify` | Runtime-verify a PR by booting the dev server and driving the UI via Chrome DevTools MCP |

## Available Agents

Agents in `agents/`:

| Agent | Description |
|-------|-------------|
| `code-reviewer` | Five-axis code review (correctness, readability, architecture, security, performance) |
| `security-auditor` | Threat-model-focused security review (OWASP, STRIDE, LLM Top 10) |
| `test-engineer` | Test coverage analysis, test design, Prove-It pattern for bugs |

## Available Skills

Skills in `skills/`:

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
| `performance-optimization` | Measure-first performance optimization |
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

## Command → Skill Mapping

| Command | Primary Skill | Also invokes |
|---------|---------------|-------------|
| `spec` | spec-driven-development | — |
| `plan` | planning-and-task-breakdown | interview-me, spec-driven-development (PRD mode) |
| `build` | incremental-implementation | test-driven-development, quality-gate, frontend-ui-engineering (UI tasks) |
| `test` | test-driven-development | quality-gate |
| `review` | code-review-and-quality | code-reviewer agent, security-auditor agent, security-and-hardening |
| `simplify` | code-simplification | — |
| `ship` | shipping-and-launch | code-reviewer agent, security-auditor agent, test-engineer agent, quality-gate, observability-and-instrumentation |
| `learn` | learn-from-session | — |
| `triage` | — (self-contained) | — |

## Authoring Rules

### Skill Authoring Rules

**Frontmatter** — `name` (kebab-case, matches directory) and `description`
(must start with "Use when...", no workflow steps).

**Body sections** (in order):
1. Core Workflow — numbered steps with exit conditions
2. Common Rationalizations — table of excuses vs reality
3. Red Flags — behavioral warning signs
4. Verification — evidence-based checklist
5. Constraints — MUST DO / MUST NOT DO
6. Reference Guide — table of reference files with load conditions

**Progressive disclosure** — keep SKILL.md focused (under 300 lines). Push
detailed guidance to `references/` files loaded on demand. No empty reference
directories. Reference files must be co-located inside the skill's own `references/`
directory — do not reference files outside the skill.

### Agent Authoring Rules

**Frontmatter** — `name`, `description`, `mode: subagent`, and `permission` settings.
Agents should be `mode: subagent` (invoked by commands, not Tab-switchable as primary
agents). Review/audit agents should have `edit: deny` and `bash: deny` since fixes
are applied in the main context, not in the persona.

**Body sections:**
- Role description (one paragraph)
- Review framework / approach (domain-specific)
- Output format (structured template)
- Rules (behavioral constraints)
- Composition section (invocation rules — see `agents/README.md`)

**Composition rule** — agents do not invoke other agents. Orchestration belongs to
slash commands. If an agent finds something warranting a different perspective, it
surfaces that as a recommendation in its report.

## Operating Behaviors

1. **Surface assumptions** — state them explicitly before implementing
2. **Manage confusion actively** — STOP, name it, present tradeoffs
3. **Push back when warranted** — not a yes-machine; sycophancy is a failure mode
4. **Enforce simplicity** — "If you build 1000 lines and 100 would suffice, you have failed"
5. **Maintain scope discipline** — touch only what you're asked to touch
6. **Verify, don't assume** — evidence required, not "seems right"