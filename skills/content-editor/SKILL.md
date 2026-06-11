---
name: content-editor
description: |
  Use when running the autonomous content pipeline: scanning a content queue
  (e.g. GitHub issues with labels), extracting source material from URLs and
  topics, evaluating article ideas against the Definition of Ready, checking
  for related existing content, loading the project's writing style guide,
  writing the article, running SEO and humanizer quality passes, verifying the
  build, and publishing through pull requests. Also handles self-learning
  (extracting patterns from reviewer feedback) and periodic content pruning
  (flagging stale articles).
category: content
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - WebFetch
  - WebSearch
  - Skill
  - Question
---

## Core Workflow

### Step 1 — Scan the content queue

Scan the content queue (defined in Reference: Content Workflow — label state
machine) for items needing processing:

- **New source items** — items with the source label that have URLs or topics
  not yet ingested
- **Ready-to-write items** — items with the brief label that meet the
  Definition of Ready
- **Items needing human input** — items with the human-input label waiting
  for the maintainer to fill gaps

Close stale items: any source-labeled item with no updates for more than
6 months gets a stale label and is closed.

### Step 1.5 — Load writing style guide

Check for the project's `docs/writing-style-guide.md`. If it doesn't exist,
flag the issue and suggest adding one (the content-editor cannot produce
project-appropriate articles without it). Load it — it defines the content
path, build command, section mapping, frontmatter rules, slug format, writing
style, link conventions, and PR format. All subsequent steps use these rules.

### Step 2 — Ingest sources

For each new source item with URLs or topics, load Reference: Source Ingester
and follow its extraction logic:

1. Identify the source type (YouTube, Spotify, article URL, plain text)
2. Extract the source material — transcript, key claims, notable quotes
3. Compute a suggested article angle based on the material
4. Post a structured extraction comment on the queue item
5. Advance the item: remove source label, add brief label

If no transcript or material was available (e.g. YouTube captions disabled),
note this in the extraction comment and ask the maintainer for key takeaways.

### Step 3 — Evaluate readiness

For each item now bearing the brief label (including ones just promoted in
Step 2):

Read the item body. Check the Definition of Ready criteria from the Content
Workflow reference. Both of these editorial fields must have real content
(not blank, not a placeholder):

- `## My opinion / take on this`
- `## Why this matters to my audience`

If both fields have content → DoR is met. Proceed to Step 4 (priority queue).

If either field is blank or placeholder → DoR is not met. Add the human-input
label. The item will be rechecked on the next run after the maintainer fills
the gaps.

### Step 4 — Prioritise the queue

From the set of brief-labeled items WITHOUT the human-input label (DoR met),
pick exactly one per run. Prioritise by:

1. Oldest first (first-in-first-out from when they received the brief label)
2. Tiebreaker: items with more source material or stronger author perspective

If no DoR-met items exist, skip to Step 8 (notify).

### Step 5 — Related-content check

On the top-priority item, search the content repository for existing articles
on the same topic. Use grep across titles and tags in frontmatter, and glob
across filenames.

Decision logic:

1. **No matches** → new article
2. **Same topic + same angle + article less than 6 months old** → skip.
   Comment on the item: "We already cover this in [article]. Skipping." Close it.
3. **Same topic + same angle + article over 6 months old** → delegate to the
   content-refresh skill to update the existing article
4. **Partial overlap, different angle** → new article with cross-links to
   related content
5. **Tutorials that have gone stale** because the how-to is now common
   knowledge → flag for conversion to opinion piece (create a pruner item)

### Step 6 — Determine format

Apply the format decision rules from the Content Workflow reference:

- **Default format**: opinion-first
- **Override to tutorial** only when strict conditions are met (see reference)

Set the format label on the queue item before writing.

### Step 7 — Write and publish

Read the issue body, extraction comments, and the author's perspective
("My opinion / take on this") to form the brief. Follow the writing style
guide for all project-specific conventions.

**Writing steps:**

1. **Gather source material** — re-read extracted content from the queue item.
   For topics or notes without extracted material, treat the text as the seed.
2. **Determine section** — use the style guide's section mapping. Only ask for
   confirmation if two sections are genuinely plausible.
3. **Find related content** — search `content/` for articles to cross-link
   (the related-content check in Step 5 already ruled out duplicates, but
   cross-linking is done here).
4. **Write the article** — follow the style guide's structure, frontmatter
   rules, writing rules, and voice conventions. 800–2000 words. Cite specific
   details from source material. No filler preamble.
5. **Humanizer pass** — invoke the `writing-humanizer` skill on the file.
6. **Generate filename** — slug-ify the title per the style guide's slug
   rules. Save to `content/<section>/<slug>.md`.
7. **Cross-link analysis** — follow the style guide's link conventions:
   outbound at first mention with context (`/notebook/{section}/{slug}`), max
   2 inbound edits to existing articles (confirm with user before staging).

**Publishing steps:**

1. Run the build command from the style guide — if it fails, fix and rebuild
2. Run the SEO sanity check from the style guide (description length, H1 match,
   filename match, link resolution)
3. Create a branch, commit, push, and open a pull request following the style
   guide's PR conventions
4. Comment on the original queue item with the PR link

### Step 8 — Self-learning

Load Reference: Self-Learning. Check for feedback from the last 24 hours
across three loops:

- **Loop 1: SEO feedback** — recurring patterns in SEO review findings
- **Loop 2: PR edit feedback** — patterns from merged PR reviews
- **Loop 3: Staleness patterns** — which topics rot fastest

Save non-obvious lessons to the appropriate skill or project configuration
files, following the rate-limiting rules in the reference.

### Step 9 — Notify

Report summary:
- Items evaluated
- Items ready
- Items needing human input
- Which article was written (if any)
- What self-learning was applied (if any)
- Empty queue: "No work this run."

## Constraints

### MUST DO

- Load the project's `docs/writing-style-guide.md` before writing
- Load the Content Workflow reference before transitioning any queue item
- Load the Source Ingester reference before extracting content
- Run the humanizer after every writing pass
- Run the build command before opening a PR — if it fails, fix and rebuild
- Check for feedback each run (PR edits, SEO findings) for self-learning
- Close stale source items with no updates in 6+ months
- Follow the format decision rules — default is opinion-first

### MUST NOT DO

- Skip the style guide check
- Write an article without source material having been extracted
- Skip the build verification
- Create PRs without running quality gates
- Modify code outside the content scope
- Skip the format decision step (always apply a format label)
- Bundle multiple articles into a single PR

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Label state machine, DoR, format rules, idea template | [references/content-workflow.md](references/content-workflow.md) | Step 1 (queue scan), Step 3 (readiness), Step 6 (format) |
| Source ingestion logic (YouTube, Spotify, URLs, plain text) | [references/source-ingester.md](references/source-ingester.md) | Step 2 — Ingest sources |
| Project writing rules (sections, frontmatter, slugs, voice, links, PR conventions) | Project's `docs/writing-style-guide.md` | Step 1.5 — Load writing style guide |
| Content pruning criteria and classification | [references/content-pruner.md](references/content-pruner.md) | Periodic content health checks |
| Self-learning feedback loops and lesson storage | [references/self-learning.md](references/self-learning.md) | Step 8 — Self-learning |
