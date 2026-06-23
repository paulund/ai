# Marketing Issue Template

Use for marketing deliverables (blog posts, social threads, changelog entries, SEO assets, etc.). Bodies must describe the deliverable in domain language — no code or file paths.

```markdown
# <deliverable title>

<AFK | HITL> marketing deliverable for the <parent feature name> feature. **Parent: #<PRD number>**

## What

<One paragraph: what is being produced, in domain language.>

## Source

<Where the content comes from. The PRD, the code, a design doc, etc.>

## Constraints

<Tone, length, asset requirements, etc.>

<If launch-coupled and no launch date is set: "Blocked on: <unblock condition>. File under `blocked` until then.">
```

## Label

```bash
gh issue create --title "marketing: <deliverable title>" \
  --label "feature,blocked,<afk|hitl>,marketing" \
  --body "<this template>"
```

## AFK vs HITL for marketing

- **AFK** — derivable from the spec without judgement: changelog entries, SEO metadata + JSON-LD, landing pages for keywords, technical explainers derived from code with strong style guardrails.
- **HITL** — needs founder voice or real evidence: polemic/competitive posts, founder-voice posts, anything requiring real screenshots, cross-posting to owned channels.

When in doubt, default to HITL. Marketing copy that sounds AI-generated is worse than a missing launch post.

## Bundling rules

- **One issue per substantive deliverable** — blog post is one, X thread is one, OG image refresh is one.
- **Minor items can be bundled** — small social copy variants, alt-text batches, etc.
- **Do not mix AFK and HITL in one issue.** If a deliverable has both, split.
- **A single HITL issue bundling multiple channels is fine when launch-coupled and the social distribution is load-bearing** — e.g. a cross-post of a launch thread to LinkedIn + Bluesky.
