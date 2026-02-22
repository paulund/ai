# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a collection of Claude Code **skills** and **agents** — reusable prompt libraries that define how Claude should behave in specific domains. It is not a runnable application; there are no build or test commands.

## Structure

- **`skills/`** — Skills are loaded automatically when a task matches their frontmatter `description`. Each skill lives in its own directory with a `SKILL.md` and optional `references/` subdirectory.
- **`agents/`** — Subagent definitions (markdown files with frontmatter) for use with Claude Code's Task tool. Each agent has a specific role (e.g., `laravel-developer`, `inertia-developer`, `qa-engineer`, `senior-code-reviewer`).
- **`rules/`** — Rule files that provide conventions to be read by agents. Currently contains `skills.md` which defines skill authoring standards.
- **`commands/`** — Slash command skills (e.g., `/create-plan-issue`) that are user-invoked rather than auto-triggered.

## Skill Authoring Rules

Rules for writing skills are defined in `rules/skills.md`. Key points:

**Frontmatter** — Skills require `name` and `description` only. The `description` must start with "Use when..." and describe trigger conditions. Slash command skills (`/create-plan-issue`, etc.) need no frontmatter.

**Body — include:**
- Core Workflow — ordered steps
- Reference Guide — table of reference files with load conditions (only if reference files exist)
- Constraints — MUST DO / MUST NOT DO behavioural rules

**Body — do not include:**
- Role Definition (persona prompting adds no value with modern models)
- "When To Use This Skill" section (belongs in frontmatter description only)
- "Related Skills" section (Claude already sees all available skills from system reminder)
- Empty Reference Guide tables
- Intro blurbs that restate the skill name

## Agent Authoring Rules

Agents use frontmatter with `name`, `description`, `model`, and `color`. Unlike skills:
- Agents are invoked via the Task tool by Claude, not auto-triggered
- Agents should include a detailed `description` with examples of when to invoke them
- Agents follow a structured implementation process with explicit steps
- Agents log corrections to a learnings file when the user corrects their approach

## Existing Agents

| Agent | Purpose |
|-------|---------|
| `laravel-developer` | Implements PHP/Laravel backend from a plan file; runs `./vendor/bin/sail composer run test` (Pint + PHPStan level 8 + Pest with 100% coverage) |
| `inertia-developer` | Implements React/TypeScript/Inertia.js frontend from a plan file; runs `npm test` (ESLint + Prettier + TypeScript + Vitest) |
| `qa-engineer` | Runs both test suites, verifies 100% backend coverage, checks acceptance criteria, audits security |
| `senior-code-reviewer` | Reviews code changes across quality, architecture, security, and test coverage phases |
