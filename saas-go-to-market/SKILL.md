---
name: saas-go-to-market
description: Use when planning a go-to-market strategy for a SaaS product, defining positioning and messaging, choosing channels, planning a launch, or building a 90-day marketing plan. Produces a structured GTM document with positioning, channels, launch phases, and metrics.
---

## Core Workflow

1. **Gather product context** — ask about the product, target audience, current stage (pre-launch, early, growth), competitors, and existing traction or distribution. Do not generate a GTM strategy without this.
2. **Define positioning** — establish the problem, audience, value proposition, and differentiation. This shapes everything else.
3. **Map channels using the ORB framework** — categorise available channels into Owned, Rented, and Borrowed. Recommend a mix appropriate to the product stage.
4. **Plan launch phases** — map out pre-launch, launch, and post-launch activities in sequence.
5. **Define metrics** — set the key metrics to track for each phase.
6. **Load the GTM reference** — read `references/gtm-checklist.md` for templates and definitions.
7. **Output the GTM document** — structured plan with positioning statement, channel strategy, phase timeline, and metrics.

---

## Positioning Framework

Positioning defines who the product is for, what it does, and why it is better than the alternative. Everything else flows from it.

**Positioning statement format:**
> For [target audience] who [have this problem], [product name] is a [category] that [provides this benefit]. Unlike [current alternative], [product name] [key differentiator].

**Questions to answer before writing any marketing:**
1. Who is the primary buyer? (Job title, company size, industry)
2. What specific problem are they hiring this product to solve?
3. What do they use today instead? (Spreadsheets, competitors, nothing)
4. What is the primary reason to choose this over the alternative?
5. What is the one-sentence description a happy customer would give to a colleague?

---

## The ORB Channel Framework

Every marketing channel is one of three types. A good GTM strategy uses all three, weighted by stage.

### Owned channels (control, no algorithm dependency)
- Email list / newsletter
- Blog and SEO content
- Documentation and help content
- Community (Discord, Slack, Circle)
- Podcast

**Best for:** Long-term compounding growth. Slow to start, hard to lose.

### Rented channels (reach, but platform-dependent)
- X (Twitter)
- LinkedIn
- YouTube
- Reddit and Hacker News
- App Store / marketplace listings
- Product Hunt

**Best for:** Distribution and reach. Fast to start, algorithmic and subject to platform changes.

### Borrowed channels (third-party audiences)
- Guest posts and content partnerships
- Podcast appearances
- Conference talks and webinars
- Influencer or creator collaborations
- Integration partner co-marketing
- Press and media coverage

**Best for:** Credibility and reaching new audiences. Requires relationship-building.

**Early-stage recommendation:** Start with 1-2 rented channels (where your audience already is), build 1 owned channel (usually email), and pursue 1-2 borrowed channel opportunities per quarter.

---

## The 5-Phase Launch Approach

Most successful SaaS launches are not a single event — they are a series of increasingly public launches.

| Phase | Who it targets | Goal |
| --- | --- | --- |
| 1. Internal | Team and advisors | Find obvious gaps before anyone sees it |
| 2. Alpha | Trusted friends, existing network | Get real usage feedback, not just opinions |
| 3. Beta | Targeted waitlist or community invite | Validate core value proposition with strangers |
| 4. Early access | Broader waitlist, specific communities | Generate early social proof and case studies |
| 5. Full launch | Public — all channels | Maximum distribution with social proof ready |

Do not skip to phase 5. Each phase builds the evidence and confidence needed for the next.

---

## Launch Phases in Detail

### Pre-launch

- Landing page with email capture
- Waitlist incentive
- 3-5 beta users for case studies
- Launch content (demo, screenshots, testimonials)
- Email sequence for waitlist

### Launch day

- Email waitlist
- Post on rented channels simultaneously
- Product Hunt if ready
- Engage with responses for 48 hours
- Monitor for technical issues

### Post-launch

- **30 days:** activation and retention. Track activation milestone, fix biggest drop-off, collect churn feedback.
- **60 days:** conversion. Test pricing, improve onboarding, start SEO.
- **90 days:** acquisition. Identify best channel, double down, build case studies.

---

## Metrics by Phase

| Phase | Primary metric | Secondary metrics |
| --- | --- | --- |
| Pre-launch | Waitlist signups | Email open rate, community engagement |
| Launch | Signups (day 1 and week 1) | Traffic, conversion rate from landing page |
| First 30 days | Activation rate (users reaching core value milestone) | Trial-to-paid conversion |
| First 60 days | MRR growth | Churn rate, NPS |
| First 90 days | CAC by channel | LTV, payback period |

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| GTM Checklist | `references/gtm-checklist.md` | Always |

---

## Constraints

### MUST DO

- Ask about product, audience, stage, and competitors before generating any strategy
- Write a positioning statement before any channel or tactical recommendations
- Use the ORB framework to structure channel recommendations
- Tailor the plan to the product stage — pre-launch advice is very different from growth-stage advice
- Include specific, actionable tactics — not generic advice like "use social media"

### MUST NOT DO

- Recommend all channels — a focused strategy on 2-3 channels beats a thin presence on 10
- Skip positioning — channels and tactics chosen without a clear position produce noise, not growth
- Recommend Product Hunt as a primary strategy — it is one tactic, not a plan
- Make channel recommendations without knowing where the target audience spends time
- Include paid advertising as an early-stage primary channel without evidence of a working message
