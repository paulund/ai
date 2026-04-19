# Pricing Strategy Reference Guide

## Why Pricing Is a Strategy Decision, Not a Calculation

Most founders price by cost-plus (what does it cost me × markup?) or by guessing. Neither works well for SaaS. Pricing should reflect the value delivered to the customer — and value is usually far greater than cost.

The three axes of SaaS pricing:
1. **Packaging** — what features are in each tier?
2. **Pricing metric** — what unit do you charge for?
3. **Price point** — what are the actual numbers?

Most pricing mistakes happen on axis 2 (wrong metric) or axis 1 (wrong segmentation), not axis 3.

---

## Choosing a Value Metric

The value metric is the thing your price scales with. The right metric:
- Increases as the customer gets more value
- Is easy for the customer to predict (they can estimate their bill)
- Is hard to game without reducing value received

### Common SaaS value metrics

| Metric | Predictable? | Scales with value? | Best for |
| --- | --- | --- | --- |
| Per user / seat | Yes | When collaboration is core | Slack, Figma, Notion |
| Per usage (API calls, messages) | No (can spike) | Yes | Twilio, AWS, OpenAI |
| Per record (contacts, customers) | Yes | When audience size = value | Mailchimp, HubSpot |
| Per feature (tiers only) | Yes | Roughly | Intercom, Canva |
| Flat rate | Yes | No | Basecamp |
| Per transaction (% or fee) | No | Yes | Stripe, Shopify |

### How to choose

1. Map how customer value scales: as customers succeed with your product, what grows? Team size? Data volume? Revenue? API calls?
2. Pick the metric that tracks that growth
3. Test with 5-10 customers: can they predict what they will pay?

If customers cannot predict their bill, they will not buy, or they will churn when the bill is higher than expected.

---

## Tier Structure Templates

### Template A: Freemium + Paid

For products with network effects or virality.

| Tier | Price | Primary limit | Target user |
| --- | --- | --- | --- |
| Free | $0 | [low usage limit] | Individual, exploring |
| Pro | $X/month | [higher limit] | Active individual or small team |
| Business | $Y/month | [high limit] | Team with collaboration needs |

**Risk:** Free users never convert. Mitigate by making the free-to-paid upgrade trigger obvious (hitting a limit, unlocking a specific feature).

### Template B: Trial + Paid

For products where value is demonstrated in the trial, and free forever adds no benefit.

| Tier | Price | Target user |
| --- | --- | --- |
| Free trial (14 days) | $0 | Evaluating |
| Pro | $X/month | Individual or small team |
| Business | $Y/month | Larger team |
| Enterprise | Custom | Large org with compliance needs |

**Risk:** Trial ends before value demonstrated. Mitigate by shortening the path to "aha moment" in onboarding.

### Template C: Paid only

For B2B products where free users have meaningful cost or the product requires meaningful setup.

| Tier | Price | Target user |
| --- | --- | --- |
| Starter | $X/month | Small team, low usage |
| Professional | $Y/month | Core business use case |
| Enterprise | Custom | High volume, compliance, SLAs |

---

## Pricing Page Best Practices

### Layout

- Put tiers in a horizontal table for comparison
- Highlight the recommended tier visually (border, badge, background colour)
- Show monthly price by default, with an annual toggle that shows savings
- Annual price should show as "X/month, billed annually" not just the total

### The recommended tier

Mark one tier as "Most Popular" or "Best Value." Customers follow social proof, even implicit social proof. This should be the tier where most customers land, not necessarily the most expensive.

### Annual discount

Standard range: 15-20%. "2 months free" is psychologically more appealing than "17% discount" even though they are identical.

### FAQ section (required on pricing page)

Include answers to:
- What happens when I exceed my plan limits?
- Can I change plans at any time?
- Can I cancel at any time?
- Do you offer refunds?
- What payment methods do you accept?
- Is there a free trial?
- Do you offer discounts for startups / nonprofits / students?

### Trust signals near CTAs

- Number of customers or companies using it ("3,400+ teams")
- Security badges (SOC 2, GDPR if applicable)
- "No credit card required" if applicable
- Money-back guarantee if offering one

---

## Van Westendorp Pricing Research

Use this method with 10-20+ customers to find the acceptable price range.

Ask four questions about your product:

1. "At what price would you consider this product too cheap (so cheap you'd question the quality)?"
2. "At what price would you consider this product a bargain — great value for money?"
3. "At what price would you start to consider this product expensive, but might still buy it?"
4. "At what price would you consider this product too expensive — you would not buy it?"

Plot the responses on a chart. The acceptable price range sits between the "bargain" and "too expensive" thresholds. The optimal price point is where the "too cheap" and "too expensive" lines cross.

This does not replace testing with real payment, but it identifies the range worth testing within.

---

## Price Increase Playbook

### Before increasing prices

You need evidence that your current prices are below market value:
- Conversion rate consistently above 40% (rarely a deal-breaker)
- Minimal price objections in sales conversations
- Churn rate below 3% (customers are getting value)
- Competitors charging 2x or more for comparable products
- Product has improved significantly since initial pricing

### How to execute

1. **Grandfather existing customers** — lock them at current pricing for 12 months minimum. Reward loyalty.
2. **Give notice** — announce the change 30-60 days before it takes effect.
3. **Be direct** — "Pricing is increasing on [date]" not "We're making exciting changes to our plans."
4. **Tie to value** — connect the increase to improvements made. "Since launch, we've added X, Y, and Z."
5. **Offer an annual lock** — give existing customers the option to lock in current pricing by switching to annual before the change.
6. **Do not apologise** — confidence in your pricing signals confidence in your product.

---

## Common Pricing Mistakes

**Pricing by cost, not value**
"This costs me $20/month to run, so I'll charge $40." The cost to you is irrelevant. The value to the customer is what matters.

**Charging per seat when seats do not scale with value**
If a small team of 3 gets the same value as a team of 30, per-seat pricing penalises small teams and caps your growth with large ones.

**Starting with enterprise pricing and no self-serve**
Enterprise deals take months. If you need revenue or user feedback quickly, start with self-serve.

**Hiding pricing**
"Contact us for pricing" is a conversion killer for self-serve products. Buyers expect transparency. Sales-led products can hide pricing; product-led growth products should not.

**Too many tiers**
Four tiers creates decision paralysis. Three is the limit. If you have a legitimate enterprise tier with custom pricing, it can sit outside the standard comparison table.

**Annual billing only**
Many customers will not commit to annual without trying monthly first. Allow monthly, then convert to annual with a discount after they have experienced value.

**Free trial without defining the activation milestone**
A 14-day trial that does not define what "activation" looks like is just a 14-day countdown before churn. The trial should guide users to the moment they experience core value, as fast as possible.
