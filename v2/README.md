# v2 — Commands & Skills (revised)

A three-layer system of reusable AI agent workflows organized by SDLC phase.

## Commands

| Command | Phase | Description |
|---------|-------|-------------|
| `/spec` | Define | Spec before code — PRD with acceptance criteria |
| `/plan` | Plan | Small, atomic tasks with dependencies |
| `/build` | Build | One thin vertical slice at a time with TDD |
| `/test` | Verify | RED→GREEN→REFACTOR; Prove-It pattern for bugs |
| `/review` | Review | Five-axis code review (correctness, readability, architecture, security, performance) |
| `/code-simplify` | Simplify | Clarity over cleverness — deletion test, AHA |
| `/ship` | Ship | Pre-launch checklist via parallel persona fan-out |
| `/learn` | Meta | Extract session lessons to AGENTS.md or skill files |

## Skills

### Core SDLC

| Skill | Phase | Invoked by |
|-------|-------|------------|
| spec-driven-development | Define | `/spec` |
| planning-and-task-breakdown | Plan | `/plan` |
| incremental-implementation | Build | `/build` |
| test-driven-development | Verify | `/build`, `/test` |
| code-review-and-quality | Review | `/review` |
| code-simplification | Simplify | `/code-simplify` |
| shipping-and-launch | Ship | `/ship` |
| learn-from-session | Meta | `/learn` |

### Cross-cutting

| Skill | When to use |
|-------|-------------|
| quality-gate | Before push/PR/merge — lint → typecheck → test → build |
| writing-humanizer | Edit or review text to remove AI-generated writing patterns |
| git-worktree | Create isolated worktrees or clean stale ones |
| ops-architecture-review | Surface and file architecture friction as GitHub issues |
| content-editor | Run autonomous content pipeline |
| context-engineering | Feed agents the right context at the right time |
| doubt-driven-development | Adversarial fresh-context review of non-trivial decisions |

### Standards

| Skill | Language/Framework |
|-------|-------------------|
| standards-api | RESTful API design conventions |
| standards-laravel | Laravel backend conventions |
| standards-nextjs | Next.js 15 App Router conventions |
| standards-php | PHP 8.3+ best practices |
| standards-typescript | TypeScript project conventions |

## Agents (Personas)

| Agent | Role | Used by |
|-------|------|---------|
| code-reviewer | Five-axis code review | `/review`, `/ship` |
| security-auditor | Security-focused audit | `/review`, `/ship` |
| test-engineer | Test coverage verification | `/test`, `/ship` |
