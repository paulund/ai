---
name: saas-pricing-strategy
description: Use when making SaaS pricing decisions, structuring pricing tiers, choosing a value metric, planning a price increase, or designing a pricing page. Produces a pricing recommendation with tier structure, value metric rationale, and pricing page guidance.
---

## Core Workflow

1. **Gather context** — ask about the product, target market, go-to-market motion (self-serve vs sales-led), current pricing (if any), conversion rate, churn rate, ARPU, and goals.
2. **Choose a value metric** — identify what unit to charge for. This is the most important pricing decision.
3. **Design tier structure** — apply the Good-Better-Best framework to define entry, recommended, and premium tiers.
4. **Recommend pricing psychology** — anchoring, decoy, and charm pricing where appropriate.
5. **Address the pricing page** — layout, CTA placement, FAQ, annual discount.
6. **Load the pricing guide** — read `references/pricing-guide.md` for frameworks and templates.
7. **Output the pricing plan** — tier structure with feature gating, pricing rationale, and pricing page recommendations.

---

## The Value Metric Decision

The value metric is what you charge for — the unit that scales with the value the customer receives. Choosing the wrong metric is the most common SaaS pricing mistake.

| Metric | Works well when | Examples |
| --- | --- | --- |
| Per seat / per user | Value scales with number of people using it | Slack, Figma, Notion |
| Per usage | Value comes from consumption | Twilio (per message), AWS (per compute) |
| Per contact / record | Value scales with the customer's audience size | Mailchimp (per subscriber), HubSpot (per contact) |
| Per feature | Different tiers unlock qualitatively different functionality | Intercom, Canva |
| Flat rate | Simple, but does not capture value from power users | Basecamp |
| Per transaction | Value tied to customer revenue | Stripe (per transaction), Shopify (per sale) |

**The test:** As the customer gets more value from your product, does the metric you charge for increase proportionally? If yes, it is a good value metric.

---

## Good-Better-Best Tier Structure

Most SaaS products should have 3 tiers. More than 4 creates decision paralysis.

| Tier | Goal | Pricing psychology |
| --- | --- | --- |
| Entry / Free / Starter | Reduce barrier to entry, drive adoption | Lowest friction possible |
| Recommended (highlight this) | Where most customers should land | Best value, most features |
| Premium / Pro / Business | Capture high-value customers | Higher limits, advanced features, more support |

**Differentiators between tiers:**
- Feature gating (advanced features in higher tiers)
- Usage limits (storage, seats, API calls)
- Support level (email vs priority vs dedicated)
- Integrations (more available at higher tiers)
- Data retention or history

**The recommended tier:** Make one tier visually distinct as "most popular" or "best value." Most customers will choose it. Price the entry tier low enough to be accessible, the recommended tier at 3-5x the entry, and the premium tier at 2-3x the recommended.

---

## Free Tier vs Free Trial

These are different strategies with different outcomes:

| Approach | When to use | Risk |
| --- | --- | --- |
| Free forever tier | When there is a large enough user base to convert, and free users create value (network effects, referrals, SEO) | Free users never convert |
| Time-limited trial | When the product delivers clear value quickly and users need to see it | Trial ends before value is demonstrated |
| Feature-limited free | When some features have viral/sharing properties | Free tier cannibalises paid |
| No free tier | B2B with a sales motion, or when free users have meaningful infrastructure cost | Higher acquisition friction |

---

## When to Raise Prices

### Market signals suggesting prices are too low:
- No pushback or negotiation from buyers
- Competitors charge significantly more for similar value
- Sales cycle is very short (< 1 week for a significant commitment)

### Business signals suggesting prices are too low:
- Conversion rate > 40% (suggests price is not a meaningful filter)
- Churn < 3% (customers are clearly getting value)
- Revenue growth is not matching usage growth

### How to raise prices:
1. Grandfather existing customers at the old price for 12 months
2. Announce the increase before it takes effect (30 days minimum)
3. Tie the increase to a new value addition where possible
4. Do not apologise — explain the reason clearly and move on

---

## Pricing Psychology

### Anchoring
Put the most expensive tier first (left to right on a pricing page). This makes the middle tier feel reasonable by comparison.

### Decoy pricing
A third tier that makes one of the others look like better value. The professional tier at $49/month looks good when the enterprise tier is $199/month.

### Charm pricing vs round pricing
- Charm pricing ($49, $99, $199) — reduces perceived price. Works for consumer products and self-serve SaaS.
- Round pricing ($50, $100, $200) — signals premium and simplicity. Works for enterprise and high-touch sales.

### Annual discount
17-20% annual discount is standard. "2 months free" framing performs better than "17% off" even though they are the same number.

---

## Reference Guide

| Topic | Reference | Load When |
| ----- | --------- | --------- |
| Pricing Frameworks | `references/pricing-guide.md` | Always |

---

## Constraints

### MUST DO

- Ask about the product, market, and go-to-market motion before making recommendations
- Recommend a specific value metric with rationale
- Apply the Good-Better-Best framework to tier design
- Present trade-offs for each major decision — there is no universally correct answer
- Tie every pricing recommendation to the value delivered, not to cost of production

### MUST NOT DO

- Recommend specific dollar amounts without research or market context
- Design more than 4 pricing tiers
- Recommend a free tier for every product — it is a strategic choice, not a default
- Ignore the go-to-market motion — self-serve pricing is very different from sales-led pricing
- Present pricing as fixed — it should be revisited every 6-12 months as the product and market evolve
