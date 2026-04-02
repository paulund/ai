# Email Sequence Reference Guide

## Subject Line Patterns

Subject lines have one job: get the email opened. They do not need to be clever — they need to be specific and relevant.

### What works

**Curiosity gap:** Tease a specific insight without giving it away.
- "The mistake that kills most SaaS trials"
- "Why your activation rate is lower than you think"

**Direct value:** State exactly what is inside.
- "Your 5-step onboarding checklist"
- "How [Company] cut churn by 22%"

**Personal/conversational:** Sound like it came from a real person.
- "Quick question about your setup"
- "Still stuck on [feature]?"

**Specific number:** Numbers increase open rates when they are genuinely specific.
- "3 things new users miss in their first week"
- "The 15-minute setup that most people skip"

### What does not work

- "Important update from [Company Name]" — vague, ignored
- "You won't believe what we've added" — clickbait, damages trust
- "Just checking in" — the most deleted subject line in existence
- All caps or excessive punctuation — feels like spam
- The product name in every subject line — learn to vary

### Preview text rules

Preview text (the grey text after the subject line in the inbox) should:
- Extend the subject line, not repeat it
- Add a second hook or detail that reinforces the open
- Be 90-140 characters

Example:
- Subject: "The mistake that kills most SaaS trials"
- Preview: "Most teams fix the wrong thing. Here's what the data actually shows."

---

## Email Body Patterns

### The hook (first sentence)

The first sentence determines whether the rest gets read. Never open with:
- "I hope this email finds you well."
- "Just following up on my last email."
- "As a valued subscriber..."
- "We're excited to share..."

Open with the most interesting or useful thing in the email:
- "Most teams fix the wrong thing when activation drops."
- "Three days into a trial, 60% of users have never used the core feature."
- "The fastest path from signup to value in [product] is one step most people skip."

### The value section

This is the body of the email. Keep it focused:
- One story, one lesson, or one piece of advice
- Use short paragraphs (2-3 sentences max)
- Plain language, no jargon
- Specific and concrete — avoid generalities

If teaching a concept, use a real example. If telling a story, include a number or outcome. If giving advice, make it actionable in the next 10 minutes.

### The CTA

One CTA per email. One link. Make it clear what happens when they click.

**Good CTAs:**
- "Start your free setup →"
- "Book a 20-minute call →"
- "See how Acme Corp did it →"
- "Complete your profile →"

**Bad CTAs:**
- "Click here"
- "Learn more"
- Three different links to different things

If you want them to do more than one thing, that is two emails, not one.

---

## Email Length by Type

| Email type | Recommended length | Why |
| --- | --- | --- |
| Transactional (welcome, confirmation) | 50-100 words | Get to the point, confirm the action |
| Onboarding / activation | 100-150 words | Direct, focused on one next step |
| Nurture / educational | 200-350 words | Enough space to teach one thing properly |
| Case study or story | 250-400 words | Stories need room to land |
| Re-engagement | 75-125 words | Short, honest, low pressure |

Longer is not more persuasive. It is easier to delete.

---

## Timing Guidelines

These are starting points, not rules. Adjust based on engagement data.

### Welcome sequences

- Email 1: Immediately (within seconds of signup)
- Email 2: Day 2 (48 hours after signup)
- Email 3: Day 4
- Email 4: Day 7
- Email 5+: Every 2-3 days

Never send more than one email per day. Respect the inbox.

### Trial onboarding

- Trigger emails based on behaviour where possible (completed setup? Triggered email A. Still stuck on day 3? Triggered email B.)
- If behaviour-triggered is not possible, use day-based timing and keep it tight — trial windows are short.

### Lead nurture

- Start with higher frequency (every 2-3 days for the first two weeks)
- Slow to weekly once the initial sequence is complete
- Remove anyone who clicks nothing after 30 days and move to re-engagement

---

## Platform Compatibility Notes

Write email body copy that works in both HTML and plain text:
- Short paragraphs (never walls of text)
- No markdown syntax in the body (some clients render `**bold**` as literal asterisks)
- Links as full text or clearly labeled CTAs, not anchor text
- Test in plain text format before sending

Common platforms and their primary strengths:
- **Resend / Postmark** — best for transactional emails, excellent deliverability
- **Customer.io / Brevo** — behaviour-triggered sequences, robust automation
- **Kit (formerly ConvertKit)** — creator audiences, simple sequences
- **Mailchimp** — broad use, good templates, limited automation on lower tiers

---

## Deliverability Basics

Good copy does not matter if emails land in spam:
- Use a verified custom sending domain (SPF, DKIM, DMARC records set)
- Do not use spam trigger words in subject lines: "FREE", "Act Now", "Limited Time", "100% guaranteed"
- Keep image-to-text ratio balanced (heavy image emails get filtered)
- Maintain a clean list — remove hard bounces and long-term non-openers
- Authenticate your domain before launching any sequence
