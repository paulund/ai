# Skills

## Frontmatter

- Required: name and description only
- Description must: start with "Use when..." and describe
trigger conditions, not what the skill is
- Slash command skills (/implement, /learn, etc.): no
frontmatter needed — user invokes them explicitly, they don't
auto-trigger

## Body — include

- Core Workflow — ordered steps
- Reference Guide — table of reference files with load
conditions (only if reference files actually exist)
- Constraints — MUST DO / MUST NOT DO behavioural rules

## Body — do not include

- Role Definition — persona prompting ("You are a senior...") adds no value with modern models
- When To Use This Skill — body is only loaded after triggering; this info belongs in frontmatter description only
- Related Skills — Claude already sees all available skills from the system reminder; listing them again is redundant
- Empty Reference Guide tables — remove the section entirely if there are no real reference files
- Intro blurbs — one-line summaries that just restate the skill name ("A senior PHP developer specialising in...")
