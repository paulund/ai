# v2 — Agent Guidance

This is v2 of the AI agent commands and skills library. It follows a three-layer
architecture: **commands** (human-invoked) → **agents** (role-specific personas)
→ **skills** (reusable workflows).

## Directory Structure

```
v2/
├── commands/     # 9 user-facing slash commands (thin orchestrators)
├── skills/       # 30 skill directories (reusable workflows)
├── agents/       # 3 role-specific personas + README.md
└── AGENTS.md     # this file
```

Reference files live inside each skill's own `references/` directory — there is no
top-level shared `references/` directory. This ensures skills are self-contained and
surive symlink-based distribution via `bin/sync`.

## Commands vs Skills vs Agents

| Layer | Invoked by | Purpose |
|-------|-----------|---------|
| **Command** | Human only | Thin orchestrator that routes to skill(s). Loaded on demand. |
| **Agent** | Command or human | Role-specific persona with perspective + output format. |
| **Skill** | Agent, command, or human | Reusable workflow with steps, constraints, and exit criteria. |

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

## Skill Authoring Rules

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

## Agent Authoring Rules

**Frontmatter** — `name` and `description` (same as skills).

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

## Distribution: bin/sync

The `sync.toml` config (gitignored — copy from `sync.example.toml`) defines:

- **source_dir** — source directory for commands/skills/agents (set to `v2`)
- **Global targets** — directories for global command/skill/agent symlinks
- **Global install lists** — which commands/skills/agents to install globally
- **Projects** — per-project paths and their desired commands/skills/agents

Usage:

```
bin/sync              # install/update all symlinks
bin/sync --dry-run    # preview changes only
bin/sync --clean      # remove stale symlinks, skip install
```

Standards skills (`standards-api`, `standards-nextjs`, `standards-typescript`,
`standards-laravel`, `standards-php`) are project-specific and should be installed
per-project, not globally. General-purpose skills are installed globally.