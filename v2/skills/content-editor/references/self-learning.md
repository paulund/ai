# Self-Learning

Check for feedback from recent activity and extract non-obvious lessons that
make future runs smarter. Runs during Step 8 of the content-editor workflow.

---

## Configuration

```yaml
# Rate limiting — max updates per file per time window
max_updates_per_skill_file: 1        # per week
max_updates_per_agents_md: 1         # per week

# Lookback windows
seo_lookback_hours: 24
pr_edit_lookback_hours: 24
slowness_scan: "every-run"           # runs after each prune/evergreen pass
```

---

## Loop 1: SEO Feedback

Check recent SEO review findings from the last 24 hours. Identify recurring
patterns:

- Missing descriptions in certain sections → update the content-draft skill
  with section-specific rules
- Tag drift → merge duplicate tags (create a queue item if needed)
- Recurring broken links → add a check to the SEO reviewer skill

Only save non-obvious lessons. Ignore standard best practices the writing
skills already handle.

---

## Loop 2: PR Edit Feedback

Check for recently merged content PRs (last 24 hours). For each merged PR,
diff the article between the PR branch and main after merge:

- What did the maintainer change? (additions, removals, rewordings)
- Extract patterns: voice corrections, structural changes, unnecessary
  preamble that was removed
- Save actionable lessons to:
  - Project AGENTS.md: high-level content strategy changes
  - Relevant writing skill files: specific writing rules or style corrections

Do NOT save one-off corrections. Only extract patterns — things the maintainer
changed repeatedly or with clear reasoning.

---

## Loop 3: Staleness Patterns

After each prune and evergreen run: which topics rot fastest?

- Avoid writing version-locked tutorials for sections where code goes stale
  quickly
- Prefer opinion or hybrid formats for topics that change frequently

Record observations as notes in the project AGENTS.md under a "content
staleness" section if the pattern is novel and actionable.

---

## Where Lessons Are Saved

| Type of lesson | Destination | Format |
|----------------|-------------|--------|
| Writing style / voice rules | Project `docs/writing-style-guide.md` | Append to a "Learned Rules" section |
| SEO patterns | The SEO reviewer skill's SKILL.md | Append to checklist or rules |
| Content strategy / high-level changes | Project AGENTS.md | Add or update relevant section |
| Framework / tool lessons | Project `docs/writing-style-guide.md` | Append to a "Learned Rules" section |

## Rules

- Append to a "Learned Rules" section in skill files. Never overwrite core
  workflow.
- If a learned rule contradicts an existing rule: prefer the newer rule (it
  reflects current context). Add a note explaining the override.
- Rate-limit: update each file at most once per week.
- Only save non-obvious lessons. Ignore standard best practices and one-off
  tweaks.
