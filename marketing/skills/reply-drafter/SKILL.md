---
name: reply-drafter
description: Use when drafting replies to social media posts on X or LinkedIn that add genuine value to the conversation. Produces 2-3 reply options with different angles for direct copy-paste.
---

## Core Workflow

1. **Read the original post** — the user pastes the post text or describes it.
2. **Identify the core claim or question** — what is the post actually saying or asking?
3. **Determine the platform** — X or LinkedIn. Ask if not clear from context.
4. **Evaluate whether a reply is worth drafting** — see evaluation criteria below.
5. **Draft 2-3 reply variations** — each using a different angle. Apply platform-specific length rules.
6. **Invoke writing:humanizer** — if the `writing` plugin is available, run humanizer on each draft.
7. **Present inline** — output all variations with a one-line description of each angle.

---

## Evaluation Criteria

### Worth replying (draft a reply):
- Directly relates to the user's area of expertise or experience
- The user can add a specific detail, real example, or alternative perspective
- The post asks a question the user can answer from experience
- The post has genuine engagement potential and the reply adds to it

### Skip (do not draft):
- The user can only agree without adding anything new
- The post is generic motivational content
- The topic is outside the user's knowledge area
- The post already has hundreds of quality replies

If the post falls in the "skip" category, say so clearly rather than drafting a hollow reply.

---

## Reply Angles

Choose 2-3 of these for each draft set:

- **Add context** — share a related fact, detail, or experience the original did not cover
- **Respectful challenge** — offer a different angle or counter-point with reasoning, not attitude
- **Real-world example** — "I've seen this in practice — here's what happened..."
- **Specific question** — ask something that genuinely advances the conversation (not engagement bait)
- **Build on the point** — agree and extend with your own concrete observation

---

## Platform Length Rules

**X replies:**
- 1-2 sentences maximum
- Under 200 characters is ideal
- Casual tone — as if typed in the moment
- No formal structure, no bullet points
- One clear contribution per reply

**LinkedIn replies:**
- 2-4 sentences
- Slightly more considered tone than X
- Can include a brief specific example or anecdote
- No bullet points or structured formatting
- End with a genuine question if appropriate

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| Reply Conventions | `references/reply-style-guide.md` | Always |

---

## Constraints

### MUST DO

- Keep X replies to 1-2 sentences maximum
- Keep LinkedIn replies to 2-4 sentences
- Write as if typed naturally in the moment, not composed like a post
- Every reply must contribute something the original post did not say
- Match the platform's tone (casual for X, considered for LinkedIn)
- Invoke `writing:humanizer` on every draft if the writing plugin is available

### MUST NOT DO

- Write generic agreement ("Great post!", "So true!", "Love this perspective!")
- Write replies longer than the original post
- Be contrarian without providing reasoning
- Include links to the user's own content in replies
- Use the reply to promote or advertise anything
- Write to disk
- Draft replies for posts the user clearly should not engage with
