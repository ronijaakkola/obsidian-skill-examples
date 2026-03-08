---
name: obsidian-summarize-material
description: >
  Summarize a YouTube video, meeting transcript, or other material into a structured
  Obsidian note and save it to the inbox folder. Use when the user wants to add content
  to their Obsidian vault, inbox, or notes — such as "summarize this video to Obsidian",
  "add this to my inbox", "save this to my notes", or "this video to my notes". Only
  trigger when the user mentions Obsidian, inbox, or notes as the destination — a generic
  "summarize this" without that context should not trigger this skill.
---

# Summarize Material to Obsidian

Turn a transcript or other source material into a structured summary note in the user's Obsidian vault.

## Workflow

### 1. Get the material

The user provides material in one of these ways:
- A file path or attachment (transcript, meeting notes, article)
- A YouTube URL (use the `youtube-transcript` skill to fetch the transcript first)
- Text already in context from a previous step

If you receive a YouTube URL, fetch the transcript before proceeding. The youtube-transcript skill outputs timestamped lines in `[HH:MM:SS] text` format.

### 2. Get the video/content title

YouTube transcripts don't include the video title. Try to fetch it from the YouTube page using WebFetch. If that fails, ask the user. The title becomes both the filename and context for the summary.

For meetings, ask the user for a descriptive title if one isn't obvious from the material.

### 3. Extract key points

Read through the material and identify the most interesting, important, or notable points. Each point should be something the user might want to revisit or dig deeper into.

Extract only what's in the material. Do not draw conclusions, add interpretations, or editorialize. The goal is to give the user a scannable outline of potentially valuable content so they can decide what to explore further.

For each key point, find a direct quote that best represents or proves the point.

### 4. Determine metadata

**Categories and topics** should be auto-extracted from the content. Prefer values that already exist in the vault. Query the vault to discover existing values rather than relying on a hardcoded list — the taxonomy evolves over time:

```bash
# List all unique categories used across the vault (includes ghost notes)
Obsidian eval code="(() => { const s = new Set(); app.vault.getFiles().forEach(f => { const m = app.metadataCache.getFileCache(f); if (m?.frontmatter?.categories) { (Array.isArray(m.frontmatter.categories) ? m.frontmatter.categories : [m.frontmatter.categories]).forEach(c => { const r = String(c).match(/\[\[(.+?)\]\]/); if (r) s.add(r[1]); }); } }); return [...s].sort().join('\n'); })()"

# List all unique topics used across the vault (includes ghost notes)
Obsidian eval code="(() => { const s = new Set(); app.vault.getFiles().forEach(f => { const m = app.metadataCache.getFileCache(f); if (m?.frontmatter?.topics) { (Array.isArray(m.frontmatter.topics) ? m.frontmatter.topics : [m.frontmatter.topics]).forEach(t => { const r = String(t).match(/\[\[(.+?)\]\]/); if (r) s.add(r[1]); }); } }); return [...s].sort().join('\n'); })()"

# Check if a source already exists in the vault
Obsidian search query="Video Title" path=sources limit=5
Obsidian search query="Video Title" path=inbox limit=5
```

If you want to use a category or topic that doesn't already exist in the vault, confirm with the user first — new categories and topics dilute the taxonomy if added carelessly.

**Authors** can be new — it's fine to create wikilinks to people who don't have a note yet.

**Published date**: include if you can determine it from the material or source. Leave empty otherwise.

### 5. Write the note

By default, save the note to `inbox/` in the vault with the content title as the filename. Use the Write tool for this since the note body is complex multi-line markdown with frontmatter — the CLI's `content=` parameter is better suited for short content.

After writing, use the CLI to verify the file was created and set any properties that are easier to handle via CLI:

```bash
# Verify the file exists
Obsidian file path="inbox/My Note Title.md"

# Open it in Obsidian for the user
Obsidian open path="inbox/My Note Title.md" newtab
```

If the user asks for the output in chat instead (e.g., they're on mobile and can't write files directly), output the complete markdown — including frontmatter — in a single fenced code block so they can copy-paste it into Obsidian. The user might say something like "just show it to me", "output to chat", or "I'm on mobile".

Before writing, read the wikilink guidelines in `references/obsidian-wikilinks.md` (relative to the project skills directory at `.claude/skills/references/obsidian-wikilinks.md`). Apply those guidelines when writing the note body — wikilink concepts, tools, and ideas that could be standalone notes.

## Output format

### Frontmatter

```yaml
---
tags:
  - inbox
  - ai-generated
categories:
  - "[[Videos]]"
topics:
  - "[[AI]]"
  - "[[Agents]]"
author:
  - "[[Speaker Name]]"
source: https://youtube.com/watch?v=VIDEO_ID
via:
published: YYYY-MM-DD
created: YYYY-MM-DD
modified: YYYY-MM-DD
---
```

- `tags`: always `inbox` and `ai-generated`
- `categories`: content type as wikilink (e.g., `[[Videos]]`, `[[Discussions]]`, `[[Talks]]`)
- `topics`: subject matter as wikilinks
- `author`: speaker or creator as wikilink. For meetings with multiple speakers, leave empty
- `source`: URL to the original material
- `via`: leave empty (user fills in later how they found it)
- `published`: date of the original material if known, otherwise empty
- `created` and `modified`: today's date in YYYY-MM-DD format

### Body structure

Start with a summary callout, then key point sections:

```markdown
> [!summary]
> Up to four sentences covering the main theme, the most significant takeaway,
> and who this material is relevant for. Keep it factual and descriptive —
> this helps the user decide whether to read the full note.

## Key point title ([MM:SS](https://youtube.com/watch?v=VIDEO_ID&t=SECONDS))

Description of the point. Can be several sentences if the point warrants it.
Explain what was discussed and provide enough context to understand why it
matters, but stick to what the material actually says.

> "Direct quote from the transcript" — [MM:SS](https://youtube.com/watch?v=VIDEO_ID&t=SECONDS)
```

### YouTube videos

For YouTube content, include two levels of timestamp links:

1. **Section heading** — links to where the topic starts being discussed. Place the timestamp in parentheses after the heading text: `## Topic title ([MM:SS](url&t=SECONDS))`
2. **Quote attribution** — links to the exact moment the quote is said

Convert `[HH:MM:SS]` timestamps from the transcript to seconds for the `&t=` parameter: hours * 3600 + minutes * 60 + seconds. Display the timestamp in `MM:SS` or `H:MM:SS` format depending on video length.

### Meeting transcripts

For meetings, there are no timestamp links. Instead, attribute quotes to the speaker with context about what was being discussed:

```markdown
> "We're at 40% coverage and shipping anyway feels irresponsible" — **Alex, when discussing whether Q2 is realistic for the public launch**
```

This helps the user recall who said what and in what context without needing to re-read the full transcript.

## Full example — YouTube video

```markdown
---
tags:
  - inbox
  - ai-generated
categories:
  - "[[Videos]]"
topics:
  - "[[AI]]"
  - "[[Agents]]"
author:
  - "[[Jane Smith]]"
source: https://youtube.com/watch?v=abc123
via:
published: 2026-02-15
created: 2026-02-22
modified: 2026-02-22
---

> [!summary]
> Jane Smith discusses where [[AI]] agents are heading in the next two years.
> She argues that agent reliability is the main bottleneck, not capability.
> The talk covers how her company approaches safety as agents gain more autonomy.
> Relevant for anyone building or designing agentic systems.

## Reliability matters more than raw capability ([13:00](https://youtube.com/watch?v=abc123&t=780))

Smith makes the case that current models are already capable enough for most
agent tasks, but fail in production because they lack consistent reliability.
She draws an analogy to self-driving cars where the last 1% of edge cases
takes 90% of the engineering effort.

> "The gap isn't about what the model can do on its best day, it's about what it does on its worst day" — [14:22](https://youtube.com/watch?v=abc123&t=862)

## Safety research needs to run ahead of deployment ([20:45](https://youtube.com/watch?v=abc123&t=1245))

The discussion shifts to how [[Acme Labs]] structures its safety research to
stay ahead of capability improvements. Smith describes a process where safety
teams get early access to new capabilities before they ship, giving them time
to identify failure modes.

> "If your safety research is always reacting to what's already deployed, you've already lost" — [22:10](https://youtube.com/watch?v=abc123&t=1330)
```

## Full example — Meeting transcript

```markdown
---
tags:
  - inbox
  - ai-generated
categories:
  - "[[Discussions]]"
topics:
  - "[[Product management]]"
  - "[[Testing]]"
author:
  -
source:
via:
published:
created: 2026-02-22
modified: 2026-02-22
---

> [!summary]
> The team met to align on Q2 launch readiness. Engineering raised concerns
> about test coverage while product pushed for earlier delivery. The group
> agreed to a phased rollout starting with internal users. No final date was
> set pending a coverage report next week.

## Disagreement on launch timeline

The team debated whether Q2 is realistic given current test coverage gaps.
Engineering sees shipping at 40% coverage as risky, while product argues
the competitive window is closing. The conversation surfaced a fundamental
tension between [[Product management|product velocity]] and engineering confidence.

> "We're at 40% coverage and shipping anyway feels irresponsible" — **Alex, when discussing whether Q2 is realistic for the public launch**

## Phased rollout as a compromise

The group converged on a phased approach: ship to internal users first, then
expand based on what breaks. This gives engineering more data while letting
product demonstrate progress.

> "Let's dogfood it for two weeks — if nothing catches fire, we open it up" — **Maria, when proposing the phased rollout approach**
```
