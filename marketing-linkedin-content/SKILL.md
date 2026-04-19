---
name: linkedin-content
description: Use when writing LinkedIn posts from an idea, work experience, observation, or topic the user provides. Produces 2-3 variations with different hooks for direct copy-paste into LinkedIn.
---

## Core Workflow

1. **Identify the input** — determine what the user has provided: a raw idea, a work experience, an observation, or a topic. Ask if unclear.
2. **Load the style guide** — read `references/linkedin-style-guide.md` before writing anything.
3. **Select hooks** — choose 2-3 different hook styles from the taxonomy below, each giving a different angle.
4. **Draft variations** — write one post per hook following the hook-story-insight-question structure.
5. **Quality check** — run each draft against the checklist below.
6. **Invoke writing:humanizer** — if the `writing` plugin is available, run humanizer on each draft.
7. **Present inline** — output all variations directly in the conversation with a one-line rationale for each.

---

## Hook Taxonomy

The hook is the first line of the post — it shows before "see more" and determines whether anyone reads the rest.

- **Problem solved** — lead with the problem and hint at the fix ("I spent 6 months debugging the wrong thing.")
- **Lesson learned** — "X years of Y taught me one thing above all others..."
- **Honest failure** — lead with what went wrong, deliver the insight from it
- **Surprising observation** — lead with something counter-intuitive you noticed ("The most productive engineers I know rarely look busy.")
- **Numbers-first** — lead with a specific metric, outcome, or timeline ("3 contracts. 1 lesson. Here it is.")
- **Sceptic converted** — "I used to think X. Then Y happened." (only with a real story, never fabricated)

---

## Post Structure

Every LinkedIn post should follow this structure:

1. **Hook** (1 line) — the most interesting or specific part of your insight. Must stand alone before "see more."
2. **Story/Context** (2-3 short paragraphs) — what happened? What did you observe? Ground it in a specific, real experience.
3. **Insight** (1-2 paragraphs) — what did you learn? What would you tell someone facing this? This is the payoff.
4. **Question** (1 line, optional) — invite responses with something specific. "Has anyone else seen this on GCP?" beats "What do you think?"

---

## Quality Checklist

Before presenting any draft, verify:

- [ ] Hook earns the "see more" click — compelling without context
- [ ] Story is grounded in a specific, concrete experience (not generic)
- [ ] Single clear insight or takeaway
- [ ] 150-300 words total
- [ ] Short paragraphs (1-2 sentences max)
- [ ] Line breaks between paragraphs for mobile readability
- [ ] 3-5 hashtags at the very end, never embedded in body text
- [ ] No AI vocabulary, no em dashes
- [ ] No corporate-speak: leverage, synergy, ecosystem, thought leader, stakeholders
- [ ] No humble-bragging ("So humbled to announce...")
- [ ] No code blocks (LinkedIn does not render them)
- [ ] British English throughout

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| LinkedIn Style | `references/linkedin-style-guide.md` | Always — before writing any post |

---

## Constraints

### MUST DO

- Write in British English
- Present 2-3 complete variations, each with a different hook
- Output all content inline in the conversation, formatted for direct copy-paste into LinkedIn
- Follow the hook-story-insight-question structure
- Include 3-5 relevant hashtags at the end of each variation
- Invoke `writing:humanizer` on every draft if the writing plugin is available
- State the hook style used for each variation (one line)

### MUST NOT DO

- Write to disk or assume any directory structure exists
- Embed hashtags in body text
- Use code blocks
- Copy an X post format directly (different audience, different tone)
- Use engagement bait ("Like if you agree, comment if you disagree")
- Write more than 300 words unless the user specifically asks for long-form
- Fabricate stories or invent experiences — only real situations
- Assume the user's industry, role, or seniority — ask if unclear
