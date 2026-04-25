---
name: marketing-x-content
description: Use when writing X/Twitter posts, threads, or long-form articles from an idea, news item, article URL, or topic the user provides. Produces 2-3 variations with different hooks and formats for direct copy-paste into X.
---

## Core Workflow

1. **Identify the input** — determine what the user has provided: a raw idea, a news item, an article to promote, or a topic to riff on. Ask if unclear.
2. **Identify the format** — single post, thread (3-5 posts), or long-form X article. Default to single post unless the topic demands more depth.
3. **Load the style guide** — read `references/x-style-guide.md` before writing anything.
4. **Select hooks** — choose 2-3 different hook styles from the taxonomy below, each giving a different angle on the same content.
5. **Draft variations** — write one variation per hook. Each draft should be complete and copy-paste ready.
6. **Quality check** — run each draft against the checklist below.
7. **Invoke writing:humanizer** — if the `writing` plugin is available, run humanizer on each draft to strip AI patterns.
8. **Present inline** — output all variations directly in the conversation with a one-line rationale for each hook choice.

---

## Hook Taxonomy

Choose 2-3 of these for each piece of content:

- **Curiosity** — challenge an assumption or tease a discovery ("Most people get this wrong...")
- **Story** — personal experience opener ("Last week I discovered...", "Six months ago I...")
- **Feature highlight** — lead with the key fact or insight, no preamble
- **Contrarian** — disagree with a common view, explain why ("Everyone says X. But actually...")
- **Numbers/data** — lead with a specific figure, timeline, or outcome ("3 years. 40 clients. One lesson.")
- **Problem-solution** — name the problem, promise the fix ("X is broken. Here's how to fix it.")
- **TIL/Discovery** — share something you just learned or noticed ("Just found out that...")

---

## Thread Structure

When writing a thread (3-5 posts):

- **Post 1**: State the topic clearly. Hook + promise of value. Make people want the rest.
- **Posts 2-N**: One idea or fact per post. Build logically, not dramatically. No cliffhangers between posts.
- **Final post**: Takeaway, implication, or honest question. Not clickbait. Not "follow for more."

Keep threads to 3-5 posts. If the topic needs more, consider a long-form article instead.

---

## Quality Checklist

Before presenting any draft, verify:

- [ ] Hook in the first line — does it earn the read on its own?
- [ ] One clear idea per post (or per thread post)
- [ ] Single takeaway the reader can act on or share
- [ ] Formatted for copy-paste: dashes (`-`) for lists, not bullet dots, no markdown that X cannot render
- [ ] No AI vocabulary: pivotal, testament, groundbreaking, delve, landscape, underscore, tapestry
- [ ] No em dashes — use commas, periods, or parentheses instead
- [ ] No hashtag spam — 0 by default, 1 maximum only if it genuinely adds context
- [ ] British English spelling throughout
- [ ] Passes the "would a real person post this?" test

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| X Post Style | `references/x-style-guide.md` | Always — before writing any post or thread |

---

## Constraints

### MUST DO

- Write in British English
- Present 2-3 complete variations, each with a different hook
- Output all content inline in the conversation, formatted for direct copy-paste into X
- Use dashes (`-`) for any lists inside posts
- State the hook style used for each variation (one line)
- Invoke `writing:humanizer` on every draft if the writing plugin is available

### MUST NOT DO

- Write to disk or assume any directory structure exists
- Use hashtags unless the user specifically requests them
- Use emoji walls, manufactured urgency, or engagement bait ("Like and retweet if you agree")
- Use sarcasm, fake storytelling ("I used to be terrible at X. Then one day..."), or humble-bragging
- Open with "Hot take:" or newspaper headline framing
- Add a "follow me for more" closing
- Assume knowledge of the user's brand, industry, or audience — ask if unclear
