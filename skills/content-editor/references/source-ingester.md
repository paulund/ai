# Source Ingester

Extract source material from URLs and topics in content-queue items and post
a structured extraction comment summarising the key claims, quotes, and a
suggested article angle.

---

## Source Type Identification

Parse the item body for URLs and classify:

| Pattern | Source Type | Extraction Method |
|---------|-------------|-------------------|
| `youtube.com/watch` or `youtu.be/` | YouTube video | Captions or page metadata |
| `open.spotify.com/episode` or `open.spotify.com/show` | Spotify podcast | Metadata + YouTube cross-search for transcript |
| `open.spotify.com/playlist` | Spotify playlist | Skip — no transcript to extract |
| Other URLs (blog, article, docs) | Web page | Use WebFetch to read |
| No URLs | Plain text topic | Web research |

---

## Extraction by Source Type

### YouTube

Try, in order:

1. `yt-dlp --write-auto-subs --sub-format srv1 --skip-download <URL>` —
   auto-generated captions
2. If no auto captions: `--write-subs` for manual captions
3. If no captions at all: fetch the video page and extract the description
4. If description is too thin: read top 5 YouTube comments via WebFetch
5. If all fail: extract title, channel, thumbnail from the page metadata

### Spotify Podcast Episode

1. Fetch episode metadata via Spotify API or WebFetch the episode page
2. Extract: show name, episode title, description, duration
3. Cross-search YouTube for the same show + episode title using WebSearch
4. If found on YouTube: pull the YouTube transcript as above
5. If not found: work from the Spotify episode description only

### Web Page (article, blog, docs)

1. Use WebFetch to retrieve the page
2. Extract: title, author, publication date, main content
3. Summarise the core argument or technique

### Plain Text (no URL)

1. Use WebSearch to research the topic
2. Read top 2–3 authoritative sources
3. Synthesise: core arguments, specific facts/numbers, alternative viewpoints

---

## Extract Key Claims

Read the full source material. Extract:

- **Key claims (3–5)**: specific techniques, arguments, or announcements.
  Include context and timestamp if available.
- **Notable quotes**: verbatim quotes worth citing. Include speaker and
  timestamp.
- **Connections to existing content**: scan the content repository's tags and
  sections for overlap. Note related articles.
- **Suggested article angle**: a concrete suggestion for what the article
  could say. Opinion-first — what might the author think about this?
- **Verdict**: Ready to write / Needs more context / Not enough substance

---

## Extraction Comment Format

Post the extraction as a comment on the queue item:

```markdown
## Extracted from: [source title](URL)
**Type:** YouTube / Podcast / Web Research
**Duration:** if applicable

### Key Claims
- Claim 1 with context (timestamp)
- Claim 2...

### Notable Quotes
> "quote" — Speaker (timestamp)

### Connection to Existing Content
Related tags: [tag1, tag2]
Related existing articles: [links]
Suggested section: [section]

### Suggested Angle
[Brief: what could an article about this say?]

### Verdict
[Ready to write / Needs more context / Not enough substance]
```

If no transcript was available (e.g. YouTube captions disabled), note this
in the comment and ask the maintainer for key takeaways.
