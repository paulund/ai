---
name: marketing-content-repurposer
description: Use when repurposing a blog post, article, newsletter, video transcript, or long-form content into social media posts for X, LinkedIn, or both platforms. Takes existing content and creates platform-optimised social posts that stand alone without requiring the reader to have seen the source.
---

## Core Workflow

1. **Accept the source** — the user provides a URL, pasted text, or description of the content.
2. **Extract the core** — identify the single most useful, surprising, or actionable insight in the source. This becomes the anchor for all posts.
3. **Ask which platforms** — if not specified, ask whether the user wants X, LinkedIn, or both.
4. **Check content pillars** — if the user has defined content pillars (themes they post about consistently), ask which pillar this piece falls under. Use it to frame the angle.
5. **Load the relevant style guides** — read the reference files for each target platform before drafting.
6. **Draft 2-3 variations per platform** — each variation uses a different hook style. Follow the platform's conventions exactly (x-content rules for X, linkedin-content rules for LinkedIn).
7. **Quality check** — verify each post stands alone, adds perspective beyond the source, and meets platform formatting rules.
8. **Invoke writing:humanizer** — if the `writing` plugin is available, run humanizer on each draft.
9. **Present grouped by platform** — output all variations inline, grouped by platform, with a one-line rationale per variation.

---

## Content Extraction Principles

The goal is never to summarize the source. Every post must add something:

- **Articles and how-to guides** — lead with the problem the article solves, not what the article says
- **News items** — lead with why it matters to the reader, not what happened
- **Your own work or projects** — lead with the outcome or lesson, not the process
- **Data or research** — lead with the most surprising or counter-intuitive finding
- **Opinion pieces** — extract the specific, concrete claim, not the general theme

Ask: "What is the one thing someone should take away from this, even if they never read the source?"

---

## Content Pillars

If the user has defined content pillars (3-5 recurring themes in their brand), use the relevant pillar to frame the angle. For example:

- A pillar of "engineering leadership" shapes a post toward lessons for managers
- A pillar of "developer productivity" shapes it toward practical tools or habits
- A pillar of "indie business" shapes it toward commercial and operational lessons

If the user has not defined pillars, ask once — it helps produce more consistent, on-brand output.

---

## Platform Conventions (summary)

**X posts:**
- Lead with hook in first line
- One idea per post
- Dashes for lists, no markdown
- 0-1 hashtags
- British English

**LinkedIn posts:**
- Hook earns the "see more" click (first line must stand alone)
- Story/context → insight → optional question
- 150-300 words
- 3-5 hashtags at the end
- British English

Full conventions in the reference files.

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| X Style | `references/x-style-guide.md` | Repurposing for X |
| LinkedIn Style | `references/linkedin-style-guide.md` | Repurposing for LinkedIn |
| Content Sourcing | `references/content-sourcing-guide.md` | Always — extraction principles |

---

## Constraints

### MUST DO

- Ask which platforms to target if not specified
- Present output grouped by platform, with 2-3 variations per platform
- Ensure every post stands alone — the reader must not need to have read the source
- Add perspective or framing beyond what the source says
- Follow each platform's formatting conventions exactly
- Invoke `writing:humanizer` on every draft if the writing plugin is available

### MUST NOT DO

- Just summarize the source content — every post must add a perspective
- Assume the user wants all platforms — ask
- Write to disk
- Create posts that require reading the source to make sense
- Add a link to the source unless the user explicitly requests it
- Use the same hook style across all variations
