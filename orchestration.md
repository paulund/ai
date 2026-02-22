# Orchestration: SDLC Workflow with Claude Code

This document defines the development workflow for this project using Claude Code's built-in Plan Mode, agents, and the `/implement` skill.

**Philosophy**: Plan first, automate implementation, enforce quality gates.

---

## Quick Start

```bash
# Step 1: Enter Plan Mode (Shift+Tab) and describe the feature
# Claude explores the codebase, asks questions, and produces a structured plan
# On exit, Claude writes plans/<feature>/plan.md and asks: GitHub Issue or /implement?

# Step 2a: Track as a GitHub Issue (implement later)
/create-plan-issue

# Step 2b: Implement now in this session
/implement

# Step 3: Review the output, merge when ready
```

---

## The Workflow

### Step 1: PLAN — Use Plan Mode

Use Claude Code's built-in Plan Mode (`Shift+Tab` to enter) to design a feature.

**What happens**:
1. Claude explores the codebase guided by your feature description
2. Claude asks questions about requirements, edge cases, and trade-offs
3. You provide answers and refine the approach
4. On exiting Plan Mode, Claude writes `plans/<feature>/plan.md` with:
   - **Feature description** — what users will see
   - **Acceptance criteria** — testable requirements
   - **Technical approach** — files to create/modify, patterns to follow
   - **Testing strategy** — what tests are needed
   - **Risks & trade-offs** — what could go wrong

**Output**: `plans/<feature>/plan.md` — the single source of truth for implementation.

The `.claude/rules/planning.md` rule ensures Claude produces this structured output automatically.

### Choose Implementation Path

After writing the plan file, present the user with this choice:

> **How would you like to proceed?**
>
> - **GitHub Issue** — run `/create-plan-issue` to record this plan as a GitHub issue. Implement it later by assigning to Claude (via `@claude` in a comment) or locally with `/implement`.
> - **Implement now** — run `/implement` to execute the full agent chain in this session.

- If the user chooses **GitHub Issue**: run `/create-plan-issue`
- If the user chooses **Implement now**: wait for the user to run `/implement` when ready

---

### Step 2: IMPLEMENT — Run `/implement`

**Command**: `/implement`

**What happens**:

```
1. Backend-developer agent spawns
   ├─ Reads plans/<feature>/plan.md
   ├─ Implements PHP/Laravel changes (models, migrations, controllers, actions, etc.)
   ├─ Writes feature tests + unit tests
   ├─ Runs ./vendor/bin/sail composer run test
   ├─ Fixes failures
   └─ Returns summary of what was implemented

2. Frontend-developer agent spawns (after backend completes)
   ├─ Reads plans/<feature>/plan.md + reviews backend code
   ├─ Implements React/TypeScript pages and components
   ├─ Regenerates Wayfinder routes
   ├─ Writes component tests
   ├─ Runs npm test
   ├─ Fixes failures
   └─ Returns summary of what was implemented

3. QA-engineer agent spawns (after frontend completes)
   ├─ Runs full test suite (backend + frontend)
   ├─ Checks 100% coverage
   ├─ Verifies acceptance criteria have test coverage
   ├─ Checks for security issues
   └─ Returns pass/fail with details

4. Senior-code-reviewer agent spawns (after QA passes)
   ├─ Reviews all changes via git diff
   ├─ Checks code quality, architecture, security
   └─ Returns verdict: APPROVE | APPROVE WITH SUGGESTIONS | REQUEST CHANGES
```

**Orchestration rules**:
- Agents run **sequentially**: backend → frontend → QA → review
- Each agent reads the **plan file** as its source of truth
- **Feedback loops**: If QA fails, the `/implement` skill re-spawns the appropriate developer agent with failure context (max 2 retries)
- **Agent models**: Backend/frontend/QA use Sonnet, reviewer uses Sonnet
- **Max turns**: Prevents runaway costs (backend: 30, frontend: 25, QA: 20)

**Output**: Code changes in your working directory. Use `git diff` to review.

**After `/implement`**:
1. Review `plans/<feature>/review.md`
2. If APPROVE: commit and merge
3. If REQUEST CHANGES: address feedback manually or re-run `/implement`

---

## Agents

Each agent runs in isolation with a fresh context. Agent definition files live in `.claude/agents/`.

| Agent | Model | Max Turns | File |
|---|---|---|---|
| backend-developer | Sonnet | 30 | `.claude/agents/backend-developer.md` |
| frontend-developer | Sonnet | 25 | `.claude/agents/frontend-developer.md` |
| qa-engineer | Sonnet | 20 | `.claude/agents/qa-engineer.md` |
| senior-code-reviewer | Sonnet | 15 | `.claude/agents/senior-code-reviewer.md` |

See individual agent files for detailed responsibilities and instructions.

---

## Skills

| Skill | Command | Status |
|---|---|---|
| `/implement` | Orchestrates agent chain | `.claude/skills/implement/SKILL.md` |
| `/create-plan-issue` | Records a plan as a GitHub issue for later implementation | `.claude/skills/create-plan-issue/SKILL.md` |

**Future skills** (not yet built):
- `/ship` — changelog, SEO, marketing content generation

---

## File Structure

```
.claude/
├── orchestration.md                  # This file
├── rules/
│   ├── planning.md                   # Guides Plan Mode output
│   ├── laravel.md
│   ├── php.md
│   ├── react.md
│   ├── testing.md
│   ├── typescript.md
│   └── css.md
├── skills/
│   ├── implement/SKILL.md            # /implement orchestration
│   ├── laravel-developer/            # Reference skill
│   ├── code-reviewer/                # Reference skill
│   └── ...
├── agents/
│   ├── backend-developer.md
│   ├── frontend-developer.md
│   ├── qa-engineer.md
│   └── senior-code-reviewer.md

plans/
├── <feature>/
│   └── plan.md                       # Source of truth (from Plan Mode)
└── ...
```
