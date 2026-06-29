---
name: project-boilerplate
description: Use when starting a new SaaS project, scaffolding /docs/ for an agentic codebase, or restructuring existing docs to be agent-readable. Generates a complete /docs/ tree plus a triggered AGENTS.md reference index, by walking the user through a structured quiz.
---

## When to use this skill

Invoke when **any** of these are true:

- Starting a new SaaS / Next.js / Node project and `/docs/` is empty or missing.
- Existing docs are scattered, stale, or not referenced from `AGENTS.md`.
- Onboarding a new repo where you can't answer "where do I document X?" without grepping.
- Preparing a codebase for agentic development — the agent needs a stable, predictable doc structure to read against.

Do **not** use for: tweaking a single doc, fixing typos, or adding one ADR. Those are single-file edits.

## Core philosophy

1. **Diff first, never overwrite blindly.** If a file exists, surface its current content and ask before replacing.
2. **Quiz > generate.** Real answers from the user beat plausible defaults. The agent should ask, not guess.
3. **Plain markdown, no frontmatter.** No YAML at the top of docs. Keep it portable.
4. **AGENTS.md is the index, /docs/ is the content.** `AGENTS.md` stays thin (≤ 250 lines) and only contains a triggered reference table — the actual content lives in `/docs/`.
5. **ADRs are immutable history.** Never edit a past ADR. Write a new one if the decision changed.
6. **One batched question per section, one section at a time.** Don't dump the whole quiz at once.

## Pre-flight inspection

Before asking any questions:

1. Read the repo root — `package.json`, `README.md`, `AGENTS.md`, `.env.example`, `wrangler.toml` / `vercel.json` / `Dockerfile` (whichever exists).
2. Walk the existing `/docs/` tree — record every file, its size, and whether it's a stub (under ~20 lines) or real content.
3. Walk `/docs/adr/` — record ADR numbers and titles.
4. Detect the stack — framework, ORM, auth provider, hosting platform, payment provider.
5. Build a **diff matrix** — for every page in the target structure (see `references/page-templates.md` for the canonical tree), note its state: `exists+real`, `exists+stub`, `missing`, `unexpected` (exists but not in target structure).

Show the user this matrix up front so they can see what they're about to be quizzed on.

## The quiz loop

For each page in the target structure, follow this exact sequence.

### Step 1 — Determine state

```
file exists and is >20 lines  → "diff" path
file exists and is ≤20 lines  → "stub" path (treat as missing)
file is missing               → "missing" path
file exists but not in target → "unexpected" path
```

### Step 2 — Branch on state

**Diff path** — show the first 30 lines of the existing file, then ask: `Update`, `Keep as-is`, or `Replace with fresh template?`

- `Update` → run the standard quiz, merge new answers into existing content.
- `Keep as-is` → skip the quiz, move on.
- `Replace` → run the standard quiz, overwrite the file.

**Stub path** — treat as missing.

**Missing path** — run the standard quiz.

**Unexpected path** — ask: `Move to <suggested path>`, `Delete`, or `Keep where it is?`

### Step 3 — Standard quiz per page

Ask **5–10 questions per page, batched in a single `question` tool call** with multiple question objects. The full question packs for every page live in `references/page-templates.md` — each template there has a "Quiz for this page" section above the file body. Load that reference when you reach the corresponding section of the loop.

Adapt wording to whatever the user already told you (don't re-ask things they answered for the overview).

### Step 4 — Write the file

For each page, write **real, opinionated content** based on the user's answers. Use the templates in `references/page-templates.md` as the structural starting point — fill placeholders with real content. Never use Lorem Ipsum. If the user skipped a section, mark it `<!-- TODO: fill -->` rather than inventing.

## The AGENTS.md regeneration step

After all docs are written, regenerate `AGENTS.md`'s **Reference Index section** from the files that now exist on disk. Use this default trigger → file mapping as a starting template; let the user override per row.

| Trigger condition | Read |
| --- | --- |
| Auth, sessions, API keys, OAuth | `docs/architecture/auth.md` |
| Adding/modifying an MCP tool | `docs/architecture/mcp/tools-reference.md` + `docs/architecture/mcp/architecture.md` |
| Schema, migrations, ORM | `docs/architecture/data-model.md` + relevant ADR |
| Billing, payments, subscription state | `docs/product/pricing.md` + `docs/business/support-model.md` |
| Reviewing/merging a PR | `docs/contributing/pr-conventions.md` |
| Prod incident | `docs/operations/runbook.md` |
| HTTP handler / external client | `docs/architecture/api-design.md` |
| Cron or scheduled work | `docs/operations/cron-jobs.md` |
| Env vars | `docs/reference/env-vars.md` |
| Domain term lookup | `docs/reference/glossary.md` |
| Error code lookup | `docs/reference/error-codes.md` |
| Planning a feature | `docs/testing/strategy.md` + relevant ADR |
| Security review | `docs/security/threat-model.md` |
| Starting a new doc / onboarding | `docs/README.md` |

**Rules:**

- `AGENTS.md` stays ≤ 250 lines after regeneration. If it would grow past that, move content into `/docs/` and link to it.
- Remove any reference to a file that does not exist on disk.
- Preserve the user's existing `AGENTS.md` content outside the Reference Index (project overview, stack, layers, commands, quality gates, conventions, skills).

## Validation step

Before reporting done, run these checks and surface results:

1. **Link integrity** — every `docs/` path in `AGENTS.md` resolves.
2. **Internal links** — every `[text](path)` inside `/docs/` resolves.
3. **Env-var coverage** — every var in `.env.example` is documented in `docs/reference/env-vars.md` (if that file exists).
4. **README completeness** — `docs/README.md` exists and links every other file in `/docs/`.
5. **AGENTS.md size** — under 250 lines.
6. **ADR format** — every file in `/docs/adr/` matches Nygard format (Status / Context / Decision / Consequences).

If any check fails, surface the failure, do not silently fix.

## Reporting template

When the skill finishes, report in this exact shape:

```
## project-boilerplate: complete

### Files created (N)
- docs/product/overview.md
- docs/architecture/data-model.md
- …

### Files updated (N)
- docs/architecture/auth.md — added MCP API key section
- docs/reference/glossary.md — expanded from 6 to 14 terms
- …

### Files kept as-is (N)
- docs/mcp/tools-reference.md
- docs/adr/006-drizzle-migration.md
- …

### Files flagged for manual review (N)
- docs/architecture/api-design.md — response envelope pattern not yet decided
- …

### Unexpected files (N)
- docs/old-notes/ — moved to docs/reference/archive/old-notes.md
- …

### Validation
- Link integrity: ✅ / ❌ (N broken links)
- Internal links: ✅ / ❌
- Env-var coverage: ✅ / ❌ (N undocumented)
- AGENTS.md size: 198 lines ✅
- ADR format: ✅

### Next steps for the user
- Review `docs/README.md` and confirm the index order.
- Triage `Files flagged for manual review` before merging.
- Commit with: `git add docs/ AGENTS.md && git commit -m "docs: scaffold project boilerplate"`
```

## Constraints

- MUST NOT overwrite a file that exists without first showing the diff and asking.
- MUST NOT write code, change `package.json`, create branches, or commit.
- MUST NOT edit past ADRs — only append new ones.
- MUST keep `AGENTS.md` under 250 lines.
- MUST use plain markdown — no frontmatter, no YAML at the top of docs.
- MUST run the validation step before reporting done.
- MUST NOT skip the diff-first branch — even if the user says "just regenerate everything," show the diff matrix first.
- SHOULD mark skipped sections with `<!-- TODO: fill -->` rather than inventing.
- SHOULD default to the Syndly / Next.js / Drizzle / Neon conventions when the repo matches that stack, since this skill was authored against it.

## Reference guide

| File | When to load |
| --- | --- |
| `references/adr-template.md` | When drafting a new ADR. |
| `references/page-templates.md` | Per-page templates AND quiz question packs. Load at Step 3 of the quiz loop. |