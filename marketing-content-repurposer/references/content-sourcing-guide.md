# Content Sourcing Guide

## Core Principle

Never just summarize. Every social post must contain your own perspective, reaction, or framing — not a description of what the source says. A post that is just a summary of an article is a worse version of the article. A post that says "here is why this matters to someone like me" is original content.

## Extraction by Source Type

### Blog post or article (yours)

You are promoting your own thinking. The post should:
- Lead with the problem the article solves, not what the article is about
- Identify the single most useful or counter-intuitive insight
- Frame it as a standalone observation, not an ad for the article
- Only include a link if the user specifically wants to drive traffic

**Ask yourself:** "What is the most useful sentence in this article, and how do I make that the entire post?"

### News item or announcement

You are reacting to something external. The post should:
- Lead with why it matters to your audience, not what was announced
- Add your own analysis or take on what it means
- Include your experience or perspective that supports your view
- Never just repeat the headline

**Ask yourself:** "What does this mean for someone in my field? What would I say about this to a colleague?"

### Personal work or project

You are sharing an experience or outcome. The post should:
- Lead with the result or lesson, not the process
- Include a specific number, timeline, or outcome where possible
- Be honest about what was hard, not just what worked
- Avoid making it sound like a case study or press release

**Ask yourself:** "What is the thing I wish someone had told me before I did this?"

### Data, research, or statistics

You are translating findings into insight. The post should:
- Lead with the most surprising or counter-intuitive finding
- Provide context that makes the number meaningful
- Include your own interpretation of what the data means
- Avoid listing multiple data points — pick one and build on it

**Ask yourself:** "If I read this stat in isolation, what would I think? What context changes that?"

### Opinion piece (external)

You are engaging with someone else's argument. The post should:
- Identify the specific, concrete claim you want to engage with
- Either agree with supporting evidence from your own experience, or disagree with reasoning
- Not hedge — have a view and defend it
- Not be a summary of the piece

**Ask yourself:** "Do I agree? Why? What have I seen that supports or contradicts this?"

## What Good Repurposing Looks Like

A long-form article on "how to reduce API response times" might generate:

- **X post** — "The single biggest cause of slow API responses I've seen: unnecessary database calls inside loops. After refactoring 3 services last month, response times dropped 60%. The fix is almost always the same: move the query outside the loop and cache the result."

- **LinkedIn post** — "I spent a week diagnosing slow API performance last month. The culprit was something I have seen in half the codebases I have worked on: N+1 queries buried inside business logic, invisible unless you are watching the query count..."

Both posts stand alone. Neither requires reading the article. Both add perspective.

## What Bad Repurposing Looks Like

- "I just published a new article on reducing API response times. Check it out at [link]."
- "In this article, I cover three techniques for improving API performance: caching, query optimization, and connection pooling."
- "Great read on API performance — highly recommend."

These are not posts. They are promotional captions. Avoid them.
