# Content Workflow

This reference defines the content pipeline's state machine, label conventions,
Definition of Ready criteria, format decision rules, and the idea creation
template. Adapt the Configuration section to your project.

---

## Configuration

Set these values for your project. The labels are the vocabulary of your
content queue (e.g. GitHub issue labels).

Writing rules (section mapping, frontmatter, slugs, voice, links, PR
conventions, content path, and build command) are defined in the project's
`docs/writing-style-guide.md`.

```yaml
# Queue configuration
queue_source: "github-issues"        # How content ideas are tracked
queue_owner: ""                      # e.g. "owner/repo"
source_label: "content-idea"         # Label for raw, unprocessed ideas
brief_label: "content-brief"         # Label for extracted, evaluable items
human_input_label: "hitl"            # Label for items needing maintainer input
stale_label: "stale"                 # Label for inactive items about to close

# Format labels (exactly one applied when writing begins)
format_labels:
  opinion: "format-opinion"          # Default — unique perspective
  tutorial: "format-tutorial"        # Step-by-step instruction
  hybrid: "format-hybrid"            # Opinion-led with examples

# PR labels
pr_label: "content-review"           # Label for content PRs

# Quality skill references
seo_skill: "writing-seo-reviewer"
humanizer_skill: "writing-humanizer"
```

---

## Label State Machine

### Queue states

| Label | Meaning | Transitions |
|-------|---------|-------------|
| `source_label` | Raw idea with source material. Not yet ingested. | → `brief_label` when ingested |
| `brief_label` | Source extracted, evaluated. Waiting for writing or maintainer input. | → `human_input_label` if DoR gaps; → writing if DoR met |
| `human_input_label` | Maintainer needs to fill editorial gaps. | → `brief_label` (remove `human_input_label`) when gaps filled |
| `stale_label` | No updates in 6+ months. Will be closed. | → closed |

### State-machine rules

- An item CANNOT have both `source_label` and `brief_label` simultaneously
- An item CANNOT have `brief_label` without exactly one format label when in
  the writing queue
- The `human_input_label` is a suspended state — the item stays in queue but
  is not picked for writing until the label is removed

---

## Definition of Ready

An item is ready to write when ALL of these are true:

| # | Criterion | How it's checked |
|---|-----------|------------------|
| 1 | Source material available | URL present OR plain text topic in the links field |
| 2 | Source extracted | Extraction comment posted by the ingester |
| 3 | Author's perspective filled | "My opinion / take on this" section has real content |
| 4 | Audience relevance filled | "Why this matters to my audience" section has real content |
| 5 | Format decided | Format label applied |

Criteria 1–2 are handled by the ingester. Criteria 3–4 are the maintainer's
responsibility — if blank, the item gets the human-input label.

A good proxy: can a writer who's never seen this material produce a worthwhile
article from this item alone? If no, it needs more work.

---

## Format Decision Rules

**Default: opinion.** The question is always "why should this NOT be an
opinion?" not "should this be a tutorial?"

Override to tutorial ONLY when ALL of these are met:

- The topic genuinely needs step-by-step instruction (teaching a skill, not
  sharing a take)
- The technique will not be automated or become common knowledge within
  6 months
- No existing good tutorial covers this
- The item author explicitly requests a tutorial

Format options:

| Label | When to use |
|-------|-------------|
| `opinion` | Unique perspective. Value is in the reasoning, not the instructions. |
| `tutorial` | Step-by-step instruction. Version-stable techniques only. |
| `hybrid` | Opinion-led article with concrete code examples. |

---

## Idea Creation Template

When creating a new item in the content queue, use this structure:

```markdown
## Links
<URL or blank>

## My opinion / take on this
<author's perspective — what you think about this topic>

## Why this matters to my audience
<blank — author fills in later>

## Format preference (optional)
<blank — editor decides>
```

---

## Transition Triggers

| From | To | Trigger | Who |
|------|-----|---------|-----|
| New item | `source_label` | Created with source material | Maintainer or idea skill |
| `source_label` (has URL/topic) | Ingest → `brief_label` | Source extracted and posted | Editor (Step 2) |
| `brief_label` (DoR gaps) | Add `human_input_label` | Editorial fields empty or placeholder | Editor (Step 3) |
| `brief_label` (DoR met) | Writing queue | Highest priority, oldest first | Editor (Step 4) |
| `human_input_label` (gaps filled) | Remove `human_input_label` | Maintainer filled editorial fields | Maintainer + Editor recheck (Step 3) |
| `source_label` (6+ months no update) | Add `stale_label` → close | No activity | Editor (Step 1) |
