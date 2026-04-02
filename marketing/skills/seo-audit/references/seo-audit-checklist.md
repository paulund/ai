# SEO Audit Checklist

## Critical (fix before anything else)

### Crawlability
- [ ] `robots.txt` exists and does not block important pages
- [ ] XML sitemap is present at `/sitemap.xml` or declared in `robots.txt`
- [ ] Sitemap contains only indexable, canonical URLs (no 301s, 404s, or `noindex` pages)
- [ ] Sitemap is submitted to Google Search Console
- [ ] No important pages are accidentally blocked with `noindex` meta tags
- [ ] Site is accessible to Googlebot (not behind a login or IP restriction)

### Indexation
- [ ] Homepage is indexed (verify via `site:yourdomain.com` search)
- [ ] Key landing pages are indexed
- [ ] No significant pages returning 404 that previously ranked
- [ ] Redirect chains are resolved (all A → B → C redirects should be A → C)
- [ ] No duplicate content serving at multiple URLs without canonical tags
- [ ] Canonical tags point to the correct preferred URL on all pages

---

## Important

### Technical foundations
- [ ] All pages served over HTTPS
- [ ] HTTP → HTTPS redirect is in place (301, not 302)
- [ ] No mixed content warnings (HTTP resources on HTTPS pages)
- [ ] Core Web Vitals within acceptable ranges:
  - LCP < 2.5s
  - INP < 200ms
  - CLS < 0.1
- [ ] Mobile-friendly (Google mobile-first indexing is the default)
- [ ] No broken internal links (4xx responses for internal links)

### URL structure
- [ ] URLs are readable and descriptive (not `/p?id=1234`)
- [ ] URLs use hyphens, not underscores
- [ ] No session IDs or excessive parameters in URLs
- [ ] URL structure is consistent across the site (trailing slash or not — pick one)
- [ ] Category and tag archive pages are either optimised or `noindex` — not both present

### On-page: title tags
- [ ] Every page has a unique title tag
- [ ] Title tags are 50-60 characters
- [ ] Primary keyword appears near the start of the title tag
- [ ] No keyword stuffing or piped lists: "Keyword1 | Keyword2 | Keyword3 | Brand"
- [ ] Brand name at the end (not the start) for non-homepage pages

### On-page: meta descriptions
- [ ] Every key page has a unique meta description
- [ ] Meta descriptions are 150-160 characters
- [ ] Meta descriptions contain the primary keyword naturally
- [ ] Meta descriptions read as a genuine description with a soft call to action

### On-page: headings
- [ ] Each page has exactly one `<h1>`
- [ ] The `<h1>` contains the primary keyword and accurately describes the page
- [ ] `<h2>` and `<h3>` tags follow logical hierarchy (no skipped levels)
- [ ] Subheadings reflect what users would search for on that page

### Internal linking
- [ ] Important pages receive internal links from relevant content
- [ ] Anchor text is descriptive ("API security guide" not "click here")
- [ ] No orphaned pages (pages with zero internal links pointing to them)
- [ ] No internal 301 redirects used as links (link directly to the canonical URL)

---

## Minor (address after critical and important)

### Image optimisation
- [ ] All images have descriptive `alt` text (not "image123.jpg")
- [ ] Alt text for images that convey information, not decorative images
- [ ] Images served in modern formats (WebP/AVIF where supported)
- [ ] Images compressed and appropriately sized for display dimensions

### Content quality signals
- [ ] Content demonstrates first-hand experience or expert knowledge (E-E-A-T)
- [ ] Author attribution on articles/blog posts
- [ ] Content is regularly reviewed and updated where information changes
- [ ] No thin content (pages under 300 words with no clear purpose)
- [ ] No keyword stuffing or unnatural repetition of target keywords

### Page speed
- [ ] Render-blocking scripts deferred or moved to bottom of body
- [ ] Images lazy-loaded for below-the-fold content
- [ ] Unused CSS/JS removed or split
- [ ] Hosting on a fast server with a CDN for static assets

---

## Common Issues by Site Type

### SaaS sites

- Feature pages often have thin content — add use cases, customer language, and specific outcomes
- Pricing page not indexed or blocked — check `robots.txt` and `noindex` tags
- Blog content targeting keywords too competitive for domain authority — find more specific long-tail targets
- App subdomain (`app.saas.com`) accidentally included in sitemap
- No decision-stage content ("alternatives", "pricing", "reviews" pages)

### Content sites / blogs

- Duplicate content from category and tag archive pages — add `noindex` or consolidate
- Keyword cannibalization — multiple posts targeting the same keyword, splitting rank signals
- Old posts never updated despite outdated information — refreshes recover rankings
- No internal linking structure — content is siloed with no link equity flow

### E-commerce

- Product pages with duplicate content from manufacturer descriptions
- Faceted navigation creating thousands of indexable filter URLs
- Out-of-stock product pages returning 404 instead of being preserved with redirects or kept live
- No schema markup for products (price, availability, reviews)

---

## Output Format for Audit Reports

Structure findings as:

```
## Critical Issues

### [Issue name]
**Page(s) affected:** [URL or pattern]
**Problem:** [What is wrong and why it matters]
**Fix:** [Specific steps to resolve]

## Important Issues
[same structure]

## Minor Issues
[same structure]

## Summary
- X critical issues (fix immediately)
- X important issues (fix within 30 days)
- X minor issues (fix as resources allow)
```
