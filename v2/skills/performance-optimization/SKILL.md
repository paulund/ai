---
name: performance-optimization
description: Use when performance requirements exist in a spec, when Core Web Vitals scores are below thresholds, when profiling reveals bottlenecks, or when users or monitoring report slow behaviour. Do not use before measuring — optimisation without data is guessing.
---

## Core Workflow

```
1. MEASURE   → Establish baseline with real data
2. IDENTIFY  → Find the actual bottleneck (not assumed)
3. FIX       → Address the specific bottleneck
4. VERIFY    → Measure again, confirm improvement
5. GUARD     → Add a CI budget check or monitoring alert to prevent regression
```

**Never skip Step 1.** Optimisation without measurement adds complexity without improving what matters.

### Step 1: Measure

- **Synthetic** (Lighthouse, DevTools Performance tab) — controlled and reproducible; good for CI regression detection
- **RUM** (web-vitals library, CrUX) — real users in real conditions; required to validate that a fix actually improved user experience

Core Web Vitals thresholds:

| Metric | Good | Poor |
|---|---|---|
| LCP (Largest Contentful Paint) | ≤ 2.5s | > 4.0s |
| INP (Interaction to Next Paint) | ≤ 200ms | > 500ms |
| CLS (Cumulative Layout Shift) | ≤ 0.1 | > 0.25 |

### Step 2: Identify the bottleneck

Use the symptom to decide where to look first:

- Slow first load → check bundle size, TTFB, render-blocking resources
- Sluggish interaction → long tasks in Performance trace (>50ms)
- Slow API → N+1 queries, missing indexes, no caching
- High CLS → images without explicit dimensions, late-loading content, font shifts

### Step 3: Fix common anti-patterns

- **N+1 queries** → use `include`/eager loading; fetch related data in one query
- **Unbounded lists** → paginate with `take`/`limit`; never `findMany()` without a limit
- **Missing image optimisation** → add `width` and `height`, `loading="lazy"` on below-fold images, `fetchpriority="high"` on the LCP image
- **Unnecessary re-renders** → stable references for objects/arrays passed as props; `React.memo` only for demonstrably expensive components
- **Large bundle** → route-level code splitting with `lazy()`, dynamic imports for heavy rarely-used features
- **No caching** → cache frequently-read, rarely-changed data with a TTL; use `Cache-Control: immutable` for content-hashed static assets

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "We'll optimise later" | Performance debt compounds. Fix obvious anti-patterns now; defer micro-optimisations. |
| "It's fast on my machine" | Your machine isn't the user's. Profile on representative hardware and slow networks. |
| "This optimisation is obvious" | If you didn't measure, you don't know. Profile first. |
| "The framework handles performance" | Frameworks prevent some issues but can't fix N+1 queries or oversized bundles. |
| "`React.memo` everywhere makes it faster" | Overusing memo adds overhead. Only apply where profiling shows it helps. |

## Red Flags

- [ ] Optimisation without profiling data to justify it
- [ ] N+1 query patterns in data-fetching code
- [ ] List endpoints without pagination
- [ ] Images without explicit dimensions or `loading="lazy"`
- [ ] Bundle size growing without review
- [ ] No performance monitoring in production
- [ ] `React.memo` and `useMemo` applied speculatively

## Verification

After any performance change:

- [ ] Before and after measurements exist with specific numbers
- [ ] The specific bottleneck is identified and addressed
- [ ] Core Web Vitals are within Good thresholds (or measurably improved)
- [ ] Bundle size hasn't increased significantly
- [ ] No N+1 queries in new data-fetching code
- [ ] Existing tests still pass (optimisation didn't break behaviour)

## Constraints

### MUST DO
- Measure before and after every optimisation.
- Use histograms/percentiles for latency — never averages.
- Paginate all list endpoints.

### MUST NOT DO
- Optimise without measurement data.
- Add `React.memo` or `useMemo` speculatively (not backed by profiling).
- Skip `loading="lazy"` on below-fold images.
- Skip `fetchpriority="high"` on the LCP image.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|----------|
| Performance Checklist | [references/performance-checklist.md](references/performance-checklist.md) | Auditing Core Web Vitals, frontend assets, backend queries, or network optimisations |
