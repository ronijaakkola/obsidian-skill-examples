# Wikilink Guidelines for Obsidian Vault

These guidelines describe how to use wikilinks (`[[...]]`) when writing notes in the user's Obsidian vault. Wikilinks create semantic connections between notes, forming a navigable knowledge graph.

## What to wikilink

Wikilink things that could reasonably be their own note — concepts, tools, claims, or ideas that carry standalone meaning.

**Specific claims and ideas** that represent a distinct thought:
- `[[Building to learn vs building to earn]]`
- `[[Large context files tend to reduce task success rates]]`
- `[[Skill-driven discovery]]`

**Tools, products, and platforms** mentioned by name:
- `[[Claude Code]]`, `[[Figma]]`, `[[Obsidian]]`, `[[Midjourney]]`

**Concepts and methodologies**:
- `[[Design process]]`, `[[Double-loop learning]]`, `[[Vibe coding]]`

**People** when they are a meaningful reference (not just mentioned in passing):
- `[[Jane Smith]]`, `[[John Doe]]`

## What NOT to wikilink

- **Quoted text** inside blockquotes — preserve quotes as-is
- **Generic descriptive words** — don't link "the team discussed" or "an interesting approach"
- **External URLs** — use standard markdown links for those
- **Code** inside backticks or code blocks
- **Common English words** that happen to match a note title

## Using aliases

When the full wikilink title reads awkwardly in a sentence, use an alias:

```markdown
This is similar to [[Vibe coding|vibe coding]] in its approach.
They work [[Become an Octopus Organization|like an octopus organization]].
```

## The heuristic

If something could be its own note with its own meaning and connections, it's worth wikilinking. Wikilinks are not just cross-references — they turn ideas into discoverable nodes in the knowledge graph. Err on the side of linking when a term represents a meaningful concept, even if the target note doesn't exist yet. Obsidian will create the link anyway, making it easy to fill in later.
