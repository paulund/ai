---
name: marketing-seo-audit
description: Use when auditing a website for SEO issues, diagnosing ranking problems, reviewing technical SEO health, or checking on-page optimisation. Produces a prioritised list of findings with specific actions.
category: marketing
---

## Core Workflow

1. Gather site context — type, goals, known issues
2. Determine scope — full, page-level, or targeted
3. Check technical foundations
4. Review on-page elements
5. Assess content quality
6. Load `references/seo-audit-checklist.md`
7. Output prioritised findings by impact

---

## Audit Priority Order

Work in this order — fixing low-priority issues while critical ones exist is a waste of time:

1. **Crawlability and indexation** — if Google cannot crawl or index pages, nothing else matters
2. **Technical foundations** — Core Web Vitals, HTTPS, canonical tags, redirects
3. **On-page optimisation** — title tags, meta descriptions, heading structure, internal links
4. **Content quality** — depth, E-E-A-T signals, keyword alignment
5. **Authority and links** — backlink profile, internal link equity distribution

---

## Technical Checks

### Crawlability
- Does `robots.txt` block any important pages or directories?
- Is the XML sitemap present, correctly formatted, and submitted to Search Console?
- Are there any `noindex` tags on pages that should be indexed?
- Are important pages reachable within 3 clicks from the homepage?

### Indexation
- Check for duplicate content (multiple URLs serving the same page)
- Check canonical tags — do they point to the correct preferred URL?
- Check for redirect chains (A → B → C, should be A → C)
- Check for 404 errors on pages that previously ranked

### Core Web Vitals targets
- **LCP (Largest Contentful Paint):** < 2.5 seconds (good), < 4 seconds (needs improvement)
- **INP (Interaction to Next Paint):** < 200ms (good), < 500ms (needs improvement)
- **CLS (Cumulative Layout Shift):** < 0.1 (good), < 0.25 (needs improvement)

### Security and structure
- All pages served over HTTPS
- HTTP → HTTPS redirect in place
- URL structure is clean, readable, and consistent (avoid query strings in URLs where possible)
- No mixed content warnings (HTTP resources on HTTPS pages)

---

## On-Page Checks

### Title tags
- 50-60 characters (longer gets truncated in SERPs)
- Unique across all pages
- Primary keyword near the front
- Describes the page content accurately — not keyword-stuffed

### Meta descriptions
- 150-160 characters
- Unique across all pages
- Contains primary keyword naturally
- Reads as a genuine description, not a keyword list
- Includes a soft CTA or value statement

### Heading structure
- One `<h1>` per page containing the primary keyword
- `<h2>` and `<h3>` create a logical hierarchy
- Subheadings reflect what users and search engines would look for on the page

### Content quality
- Does the page answer the search intent for its target keyword?
- Is the content more comprehensive or more useful than the current top-ranking pages?
- Does the page demonstrate first-hand experience, expertise, or unique insight?
- Is content updated when information changes?

### Internal linking
- Important pages receive links from other relevant pages on the site
- Anchor text is descriptive, not "click here" or "read more"
- No orphaned pages (pages with no internal links pointing to them)

---

## Important Limitation

`web_fetch` and browser-based fetching **cannot reliably detect structured data (schema markup)** because CMS plugins inject JSON-LD via JavaScript after page load. To verify structured data:
- Use Google's Rich Results Test (search.google.com/test/rich-results)
- Use Screaming Frog with JavaScript rendering enabled
- Use browser DevTools → Sources to inspect the rendered DOM

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| SEO Audit Checklist | `references/seo-audit-checklist.md` | Always |

---

## Constraints

### MUST DO

- Ask about site type, current issues, and scope before auditing
- Work through priority order — critical issues first
- Provide specific fix instructions for each finding, not just "fix your title tags"
- Group findings by impact level: critical / important / minor
- Note the structured data limitation when relevant

### MUST NOT DO

- Audit without knowing what the site does and who it serves
- Recommend fixes for low-priority issues before covering critical ones
- Claim to verify structured data via web_fetch — always recommend the Rich Results Test
- Provide keyword rankings or traffic estimates without access to Search Console data
- Recommend keyword stuffing or any black-hat technique
