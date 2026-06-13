# Content Pruner

Conservative content pruning. Identify articles that are no longer valuable
and flag them — never delete files directly. When in doubt, prefer marking
for update over deletion.

---

## Configuration

```yaml
content_path: "content/"
prune_label: "content-prune"         # Label for prune/update queue items
evergreen_label: "content-evergreen"  # Label for update-only queue items
skip_directories:                     # Directories to skip entirely
  - "releases"
  - "newsletter"
```

---

## Classification Criteria

### Keep (no action)

- Conceptual or architectural posts that don't name specific versions
- Mental-model and opinion posts (age well)
- Posts with strong internal link equity (linked from many other articles)
- Posts with recent git activity (updated within 6 months)

### Update (flag with evergreen label)

- Tutorials with version-specific code examples that are slightly outdated but
  salvageable
- Articles where the core concept is still correct but examples are old
- Articles that could be improved by converting tutorial → opinion piece

### Prune (flag with prune label — candidate for deletion)

- Tutorials where the how-to has become common knowledge or trivial
- Articles superseded by a newer, better article on the same topic
- Very short articles (< 300 words) with no unique insight
- Articles referencing APIs, tools, or services that no longer exist
- Articles with zero internal links from other content

### Convert to Opinion (flag with prune label — conversion recommendation)

- Tutorials where the core idea still matters but the how-to is now common
- Suggested action: rewrite as an opinion piece, not a how-to

---

## Skip Directories

Content in the skip directories is excluded from pruning scans. These
directories typically contain non-editorial content (release notes, archived
newsletters, etc.).

---

## Output

For each prune or conversion candidate, create an item in the content queue
with the appropriate label. Include:

- Article file path
- Why it's flagged (which criterion triggered it)
- Suggested action: delete, convert to opinion, or merge into another article
- Estimated impact: how many internal links would need updating
