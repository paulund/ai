---
name: ai-to-prd
description: Turn an idea or spec into a parent PRD plus vertical-sliced GitHub issues ready for the /ship loop, with marketing requirements captured alongside. Use when the user wants to plan a feature, break down work, or convert an idea into issues. Trigger phrases - "/plan", "plan this feature", "break this into issues".
---

# Plan

Turn an idea into a **parent PRD** plus **thin vertical-slice** GitHub issues, with **marketing requirements** captured in the same pass.

Output shape per feature:

- **1 parent PRD issue** — durable product spec using the PRD template, labelled `prd`.
- **N child slice issues** — each one shippable by `/ship` in a single PR, labelled `planned,afk,p{1,2,3}`.
- **M marketing issues** — blog posts, launch assets, social content, labelled `planned,hitl,marketing`.

## Philosophy

- **Parent PRD is the durable context.** Slices close and delete themselves; the PRD persists as the single source-of-truth for why the feature exists and how it hangs together.
- **Thin vertical slices** — each child issue cuts through every layer (schema → API → UI → tests) end-to-end. Not horizontal layers.
- **AFK over HITL** — prefer slices `/ship` can do autonomously. Mark `hitl` only when human judgement is genuinely required (design calls, sensitive UX, architectural forks).
- **Marketing is part of shipping.** A feature without launch content is a feature nobody hears about. Capture it at planning time, not as an after-thought.
- **Quiz the user before creating.** Never run ahead. Show the breakdown, get approval, then create.
- **Durable issue text.** No file paths, no line numbers, no code snippets. Describe behaviours and contracts.

## Workflow

### Phase 0 — Refine a fuzzy idea (optional)

Skip if the user has a clear spec. Only run if the input is vague or the user asks for ideation.

1. Restate as a **"How Might We…"** problem statement.
2. Ask **3–5 sharpening questions** — no more. Focus on: who is this for, what does success look like, what are the real constraints, why now.
3. Generate **2–3 variations** using lenses: simplification, inversion, 10× version.
4. Produce a one-pager: problem, recommended direction, MVP scope, explicit "Not Doing" list.

Show the one-pager, confirm, move to Phase 1.

### Phase 1 — Explore the repo

If unexplored, do it now. Understand: current architecture, existing patterns (check `CLAUDE.md`), the domain language, where the work will touch. Use the `Explore` agent for anything beyond a few files.

### Phase 2 — Draft the PRD

Sketch major modules. Actively look for opportunities to extract **deep modules** — ones that encapsulate functionality behind a simple, testable interface that rarely changes.

Fill in the PRD template (see "PRD Template" section below). In particular:

- Problem statement from the user's perspective
- Solution from the user's perspective
- Extensive user stories (long numbered list, covers every aspect)
- Implementation decisions (modules, interfaces, schema, API contracts)
- Testing decisions (what makes a good test, which modules to test, prior art)
- Out of scope
- Further notes

### Phase 3 — Check PRD with the user

Present the drafted modules + testing plan. Ask:

- Do the modules match expectations?
- Which modules need tests written for them?
- Anything missing from the user-stories list?
- Anything in scope that shouldn't be?

Iterate until approved.

### Phase 4 — Draft marketing requirements

For the feature, propose:

1. **Blog posts** — 1–3 long-form SEO posts tied to the feature. Each with:
   - Title
   - Primary keyword / search intent
   - Funnel stage (awareness / consideration / decision)
   - Target audience segment
   - Launch timing (pre-launch / launch week / post-launch)
2. **Social / X threads** — 2–5 thread hooks matching the project's content-pillar structure. Each with pillar tag + one-sentence hook.
3. **Launch assets** — screenshots, demo Loom, product GIF, OG image updates, changelog entry.
4. **SEO metadata** — landing-page meta description, canonical URL, structured-data additions.
5. **Email / newsletter** — if the project has a list, the announcement copy needs scheduling.
6. **Acquisition-channel adjustments** — any Reddit subs, Farcaster channels, community Discords where this belongs.

Ground suggestions in the project's existing content calendar / acquisition stack if one exists (look for `docs/Acquisition/**`, `docs/Idea/**`, content calendars, or similar). Don't invent new pillars that conflict with what the project already runs.

### Phase 5 — Check marketing with the user

Present the marketing items. Ask:

- Are the blog post topics right? Wrong funnel stage?
- Missing any launch asset the project typically produces?
- Channels missing / over-indexed?
- Should any marketing item block the feature launch (critical) vs follow it (nice-to-have)?

Iterate until approved.

### Phase 6 — Draft the vertical slices

Break the feature into **tracer-bullet** slices. Each is a thin end-to-end path through every layer.

**Good slicing (vertical):**
```
Slice 1: User can create a task (schema + API + UI + test)
Slice 2: User can list tasks (query + API + UI + test)
Slice 3: User can delete a task (mutation + API + UI + test)
```

**Bad slicing (horizontal):**
```
Task 1: All the database schema
Task 2: All the API endpoints
Task 3: All the UI
```

For each slice decide:
- **AFK** vs **HITL** — can `/ship` do it alone?
- **Blocked by** — real dependencies on other slices
- **Priority** — `p1` critical path, `p2` important, `p3` nice-to-have

### Phase 7 — Check slicing with the user

Present the slices as a numbered list. Per slice:
```
1. Add waitlist schema + repository
   Type: AFK
   Priority: p1
   Blocked by: none
   Acceptance: Prisma model + repository with founding-member logic, tested
```

Ask: granularity right? dependencies correct? merge/split anything? AFK/HITL correct?

Iterate until approved.

### Phase 8 — Create the parent PRD issue

Create the PRD issue first so subsequent slice issues can reference it.

```bash
gh issue create \
  --title "feat(PRD): <feature name>" \
  --label "prd,feature,<project-scope-label-if-any>" \
  --body "<PRD template filled in>"
```

Capture the issue number returned.

### Phase 9 — Create the child slice issues

Create in **dependency order** (blockers first) so `Blocked by #N` references are real numbers.

```bash
gh issue create \
  --title "feat: <slice title>" \
  --label "planned,afk,p1,feature,<project-scope-label-if-any>" \
  --body "<slice template filled in, with Parent: #<PRD number>>"
```

Use `hitl` instead of `afk` when a slice genuinely needs human input.

### Phase 10 — Create marketing issues

Create one issue per substantive marketing deliverable. Blog posts, launch assets, and campaign coordination each get their own.

```bash
gh issue create \
  --title "marketing: <what to produce>" \
  --label "planned,hitl,marketing,<project-scope-label-if-any>" \
  --body "<marketing template filled in, Parent: #<PRD number>>"
```

Minor items (X threads, OG image refreshes) can be bundled into a single "launch assets" issue rather than fragmented per-tweet.

### Phase 11 — Summarise

Print:
- Parent PRD URL
- All child slice URLs with AFK/HITL + priority
- All marketing URLs with priority
- One line on what's ready to pick up first (usually the highest-priority unblocked AFK slice)

Suggest next step: `/loop /ship` to start processing the backlog; schedule the first blog post draft yourself (HITL).

## PRD Template

Use this exactly for the parent PRD body.

```markdown
## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each in the format:

As a <user>, I want a <capability>, so that <benefit>.

1. As a <user>, I want a <capability>, so that <benefit>.
2. ...

## Implementation Decisions

- Modules to build/modify (named, with interfaces)
- Technical clarifications from the user
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

NO file paths, NO code snippets.

## Testing Decisions

- What makes a good test (external behaviour, not implementation)
- Which modules will be tested
- Prior art for the tests (similar patterns in the codebase)

## Marketing Requirements

### Blog posts
- Title — primary keyword — funnel stage — timing

### Social / threads
- Pillar — hook

### Launch assets
- What's needed

### SEO metadata
- Meta description, canonical, structured data

### Channels
- Where this is announced

## Out of Scope

What's explicitly NOT in this PRD.

## Further Notes

Dependencies on other PRDs, critical-path callouts, insurance/compliance flags.
```

## Slice Template

Use this for each child issue body.

```markdown
Parent: #<PRD number>

## What to build

End-to-end behaviour in plain language. No code, no file paths.

## Acceptance criteria

- [ ] Criterion 1 (behaviour, not implementation)
- [ ] Criterion 2
- [ ] `pnpm test` and `pnpm build` pass (adjust to project commands)

## Blocked by

#N (reason), or "None — can start immediately"

## Notes

Anything non-obvious about this slice's boundary.
```

## Marketing Issue Template

```markdown
Parent: #<PRD number>

## What to produce

E.g. "Long-form blog post: How Cairn Taxes Your Uniswap LP Position"

## Details

- Primary keyword / search intent:
- Funnel stage (awareness / consideration / decision):
- Target audience segment:
- Length / format:
- Where it publishes (blog path, X handle, newsletter):

## Launch timing

- [ ] Pre-launch teaser
- [ ] Launch week
- [ ] Post-launch follow-up

## Acceptance criteria

- [ ] Draft reviewed
- [ ] Published at <URL>
- [ ] Cross-posted to relevant channels
- [ ] Metrics baseline captured (impressions, clicks after 7d)

## Notes
```

## Durability rules

Every issue must survive refactors:

- **No file paths** — they go stale
- **No code snippets** — they rot
- **No line numbers** — same
- **Describe behaviours** — "user can undo a deletion within 5 seconds" not "add `undoDelete()` to TasksController"
- **Use domain language** — match the words the codebase and product already use
- **Readable in 30 seconds** — if longer, too big or too wordy

## What NOT to do

- Don't skip Phase 3 / 5 / 7 approvals — creating unapproved issues clogs the backlog
- Don't create issues before the user approves all three (PRD, marketing, slices)
- Don't merge marketing into the PRD body as a substitute for real issues — marketing work needs its own trackable items
- Don't pick coarse slices to "save time" — `/ship` works best with many small AFK issues
- Don't invent marketing pillars that conflict with the project's existing content calendar
- Don't skip the parent PRD — child slices need a persistent context anchor
- Don't label marketing issues `afk` — writing is HITL
