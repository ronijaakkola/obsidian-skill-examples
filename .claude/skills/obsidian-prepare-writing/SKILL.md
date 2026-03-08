---
name: obsidian-prepare-writing
description: >
  Browse the Obsidian vault to surface potential blog post topics from existing notes, then
  evaluate and rank them for publication readiness. Use when the user says "prepare writing",
  "find writing topics", "what should I write about", "blog post ideas",
  "find something to write", "surface ideas", or any variation of
  wanting to discover publishable ideas from their notes. Also trigger when the user asks to
  review their notes for writing potential, find mature thoughts worth expanding, or wants help
  choosing their next blog post topic.
---

# Prepare Writing

Surface interesting, publishable blog post ideas from the vault and evaluate them for
publication readiness. This happens in two phases: discovery and review.

Everything in this skill comes from the user's existing notes. Your job is to find and
evaluate — never to invent ideas or inject your own thinking. The user's voice and thinking
are the source material. You surface what's already there.

## Phase 1: Writing Assistant — Discover Topics

Your role is a curious assistant browsing through the user's notes to find ideas worth
developing into blog posts.

### What to look for

The vault contains two types of thought notes:

- **`#internal`** notes are the user's own original thinking. These are the primary source
  for blog post ideas because they represent the user's unique perspective.
- **`#external`** notes capture ideas from other sources (articles, talks, books). These
  aren't blog post candidates themselves, but they provide supporting material and context
  for internal ideas.

A good blog post candidate is a note (or cluster of related notes) where the user has
expressed something interesting — a genuine insight, observation, or argument that comes
from their own experience and thinking.

### How to search

Always use the `obsidian-cli` skill for vault queries. The CLI gives you structured access
to tags, properties, backlinks, and search — use it instead of raw file globbing or grepping.

1. **Get all `#internal` notes** — these are the primary candidates:
   ```bash
   Obsidian tag name=internal format=json
   ```

2. **Read promising notes** — use the CLI to read note content and check properties:
   ```bash
   Obsidian read file="Note Title"
   Obsidian properties file="Note Title" format=yaml
   ```
   Focus on notes that have substantive content (not just a single sentence or a link).

3. **Follow the links** — internal notes often link to external notes, other internal notes,
   and source material. Use the CLI to explore connections:
   ```bash
   Obsidian links file="Note Title"
   ```
   A note that connects to many other notes may indicate a rich topic with depth.

4. **Check backlinks** — notes referenced by many other notes are often central ideas:
   ```bash
   Obsidian backlinks file="Note Title" format=json
   ```

5. **Search for related content** — find notes related to a topic across the vault:
   ```bash
   Obsidian search query="topic keyword" format=json
   Obsidian search:context query="topic keyword" format=json
   ```

6. **Look for clusters** — sometimes multiple short notes orbit the same theme. A cluster of
   3-4 related notes about the same topic might individually be thin but together form a
   solid post. Use backlinks and search to find these connections.

### What makes a strong candidate

Look for notes that have one or more of these qualities:

- **Original perspective** — the user isn't just summarizing someone else; they've formed
  their own angle, critique, or synthesis
- **Connected thinking** — the note links to supporting material (external notes, sources)
  that could provide evidence or examples for the user's argument
- **Tension or contrast** — the note challenges a common assumption or puts two ideas in
  productive conflict
- **Practical insight** — the note contains wisdom from the user's own experience
- **Already well-written** — some notes are almost publication-ready, just needing structure
  and polish
- **Rich cluster** — several related notes that together form a complete argument

The user's typical blog topics include design, software development, AI, and
planning/productivity — but any topic with a genuinely interesting idea is fair game.

### Selecting candidates

After browsing, select **5-8 potential topics**. For each, note:
- The primary note(s) involved
- A one-sentence summary of the core idea **in the user's own words or closely paraphrasing**
- What makes it interesting
- What supporting material exists in the vault (linked external notes, sources)

Present these to the user in a concise list. Don't write full evaluations yet — that's
Phase 2. Just give enough for the user to see what you've found.

Ask the user: "These are the ideas I found most interesting. Want me to evaluate all of
them, or should we narrow down first?"

## Phase 2: Publication Reviewer — Evaluate and Rank

Switch roles. You are now an independent publication reviewer — an editor who evaluates
whether these ideas are worth publishing as blog posts. Your assessment is based entirely
on what the user has written in their notes. You don't add ideas or suggest new arguments;
you evaluate the strength of what's already there and identify gaps the user would need to
fill themselves.

### For each candidate, evaluate

**Novelty and interest** — Based on what the user has written, would a reader learn something
new or see something familiar in a new light? Is the user's take distinctive, or does it
read like a common opinion?

**Argument quality** — Is the reasoning in the user's notes sound? Are there logical gaps,
unsupported claims, or places where the user is asserting rather than arguing? Would a
skeptical reader find it convincing?

**Completeness** — Does the idea have enough substance to stand on its own as a post? Some
ideas are genuinely complete at 300 words; others need 1500. Judge the idea against the scope
it naturally requires, not against an arbitrary length.

**Supporting material** — Does the user have evidence in their vault (external notes, sources,
personal experiences described in memories or daily notes) that could strengthen the post?
Or is the idea currently unsupported assertion?

**Readiness** — How much work would it take to go from current state to publishable? Is it
90% there and needs editing, or 30% there and needs the user to develop the thinking further?

### Output format

Rank the candidates from most to least promising. For each one:

**[Rank]. [Topic title]**
- **Core idea**: One sentence, drawn from the user's notes
- **Primary note(s)**: The vault notes this is based on
- **Supporting material**: Related notes in the vault that provide evidence or context
- **Strength**: What's genuinely good about this idea (1-2 sentences)
- **Weakness**: What's missing or underdeveloped (1-2 sentences)
- **Verdict**: Publish-ready / needs minor work / needs significant development / not ready
- **Next steps**: A concrete task list of 3-5 things to move this toward publication.
  These should be things like "develop the example about X you mentioned in [note]",
  "connect your argument about Y to the counterpoint in [other note]", or "research Z to
  support the claim you make about W". Point the user to specific notes and gaps — the user
  does the thinking and writing.

### Tone

Be respectful but direct. The user wants honest feedback, not encouragement for its own sake.
If an idea isn't strong enough, say so and explain why. If it's great, say that too. The most
useful thing you can do is help the user focus their limited writing energy on the ideas with
the most potential.

## Phase 3: Create Efforts

After presenting the ranked evaluations, ask the user:

"Would you like me to create effort notes for any of these? If so, which ones?"

Wait for the user to choose. Then use the `obsidian-create-effort` skill to create an effort
for each selected idea. Each effort should include:

- **Goal**: writing and publishing the blog post
- **Links**: the primary note(s) and supporting material from the evaluation
- **Tasks**: the "next steps" from the evaluation, in order
- **Notes section**: the full evaluation (verdict, strengths, weaknesses, analysis) under
  `## Notes` so the user has the reviewer's perspective available when they sit down to write
- **Categories**: `[[Writing]]` plus any other relevant categories
- **Topics**: matching the topics of the source notes

Suggest categories and topics and confirm with the user before creating, as the
`obsidian-create-effort` skill requires.
