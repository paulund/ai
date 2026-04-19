---
name: humanizer
description: Use when editing or reviewing text to remove signs of AI-generated writing and make it sound more natural and human. Detects and fixes patterns including inflated significance language, promotional phrasing, superficial -ing analyses, vague attributions, em dash overuse, rule of three, AI vocabulary words, negative parallelisms, excessive hedging, and sycophantic tone. Based on Wikipedia's "Signs of AI writing" guide.
---

## Core Workflow

1. Read the input text carefully
2. Identify all AI writing patterns (see pattern catalogue below)
3. Rewrite each problematic section
4. Verify the revised text sounds natural when read aloud, varies sentence structure, uses specific details over vague claims, and uses simple constructions (is/are/has) where appropriate
5. Present the humanized version, with an optional brief summary of changes made

---

## Adding Voice

Removing AI patterns is half the job. Sterile, voiceless writing is just as obvious as slop.

**Signs of soulless writing (even if technically "clean"):**
- Every sentence is the same length and structure
- No opinions, just neutral reporting
- No acknowledgment of uncertainty or mixed feelings
- No first-person perspective when appropriate
- No humor, no edge, no personality

**How to add voice:**
- Have opinions — react to facts, don't just report them
- Vary rhythm — mix short punchy sentences with longer ones
- Acknowledge complexity — "impressive but also unsettling" beats "impressive"
- Use "I" when it fits — it signals a real person thinking
- Be specific about feelings — not "concerning" but what specifically is unsettling

---

## Pattern Catalogue

### Content patterns

**1. Undue significance inflation**
Words: stands/serves as, is a testament/reminder, vital/significant/crucial/pivotal, underscores, highlights its importance, reflects broader, symbolizing its enduring, contributing to, setting the stage for, key turning point, evolving landscape, indelible mark

Fix: Replace with plain statement of what the thing actually does or is.

**2. Notability name-dropping**
Words: independent coverage, local/national media outlets, active social media presence

Fix: Replace with a specific claim from a specific source with context.

**3. Superficial -ing analyses**
Words: highlighting/underscoring/emphasizing..., ensuring..., reflecting/symbolizing..., contributing to..., cultivating/fostering..., showcasing...

Fix: Either cut the -ing phrase or turn it into its own sentence with a real source or fact.

**4. Promotional language**
Words: boasts a, vibrant, rich (figurative), profound, enhancing its, showcasing, exemplifies, commitment to, nestled, in the heart of, groundbreaking, renowned, breathtaking, must-visit, stunning

Fix: Replace with specific, neutral description.

**5. Vague attributions**
Words: Industry reports, Observers have cited, Experts argue, Some critics argue, several sources

Fix: Name the source and the specific claim, or remove the attribution entirely.

**6. Formulaic challenges sections**
Words: Despite its... faces several challenges, Despite these challenges, Challenges and Legacy, Future Outlook

Fix: Replace with specific facts about specific problems and what was done about them.

---

### Language patterns

**7. AI vocabulary**
High-frequency post-2023 words: Additionally, align with, crucial, delve, emphasizing, enduring, enhance, fostering, garner, highlight (verb), interplay, intricate/intricacies, key (adjective), landscape (abstract noun), pivotal, showcase, tapestry (abstract noun), testament, underscore (verb), valuable, vibrant

Fix: Use plain alternatives or restructure the sentence.

**8. Copula avoidance**
Words: serves as/stands as/marks/represents [a], boasts/features/offers [a]

Fix: Replace with is/are/has.

**9. Negative parallelisms**
Pattern: "Not only...but...", "It's not just about X; it's Y", "Not merely X, it's Y"

Fix: Pick the stronger statement and cut the construction.

**10. Rule of three overuse**
Pattern: Forced groupings of three items — "innovation, inspiration, and industry insights"

Fix: Use as many items as actually exist. Two is fine. One is fine.

**11. Elegant variation (synonym cycling)**
Pattern: protagonist → main character → central figure → hero (all referring to the same thing)

Fix: Pick one term and use it consistently.

**12. False ranges**
Pattern: "from X to Y" where X and Y aren't on a meaningful scale

Fix: List the actual items or describe scope directly.

---

### Style patterns

**13. Em dash overuse**
Fix: Replace — with a comma, period, or parentheses. One em dash per paragraph is generous.

**14. Overuse of boldface**
Fix: Remove bold from inline phrases unless they are genuinely technical terms being defined.

**15. Inline-header bullet lists**
Pattern: `- **Label:** Description of label.`

Fix: Convert to prose, or use a simple list without bolded labels.

**16. Title case headings**
Fix: Use sentence case — only the first word and proper nouns capitalised.

**17. Emojis in headings or bullets**
Fix: Remove all decorative emojis.

**18. Curly quotation marks**
Fix: Replace "..." with "..."

---

### Communication patterns

**19. Chatbot artifacts**
Words: I hope this helps, Of course!, Certainly!, You're absolutely right!, Would you like..., let me know, Here is a...

Fix: Remove entirely. Start with the content.

**20. Knowledge-cutoff disclaimers**
Words: as of [date], Up to my last training update, While specific details are limited, based on available information

Fix: Either find the actual fact or cut the sentence.

**21. Sycophantic tone**
Words: Great question!, You're absolutely right!, That's an excellent point

Fix: Remove. Respond to the substance directly.

**22. Filler phrases**
- "In order to achieve this goal" → "To achieve this"
- "Due to the fact that" → "Because"
- "At this point in time" → "Now"
- "In the event that" → "If"
- "has the ability to" → "can"
- "It is important to note that" → cut entirely
- "has gotten harder/better/more X" → "is harder" / "has improved" / "is more X"

**23. Excessive hedging**
Pattern: "could potentially possibly be argued that... might have some effect"

Fix: State the claim and qualify it once if needed. "The policy may affect outcomes."

**24. Generic positive conclusions**
Words: the future looks bright, exciting times lie ahead, this journey toward excellence, a step in the right direction

Fix: End with a specific fact about what happens next, or cut the conclusion.

---

## Constraints

### MUST DO
- Preserve the core meaning of every sentence
- Match the intended tone (formal, casual, technical, etc.)
- Use specific details and named sources where the original used vague claims — if no specific detail exists, remove the vague claim rather than inventing one
- Use sentence-case headings

### MUST NOT DO
- Invent facts, sources, or quotes not present in the original
- Strip personality or opinions from first-person writing — only remove AI-ism patterns
- Add new AI vocabulary while removing old instances
- Introduce em dashes, rule-of-three constructions, or other AI patterns while fixing others
