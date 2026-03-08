---
name: obsidian-create-effort
description: >
  Create effort notes in the Obsidian vault. An effort is a project or initiative tracked with
  tasks, goals, categories, and topics. Use when the user asks to create an effort, start a
  project, track a new initiative, or when another skill needs to create an effort note.
  Trigger on phrases like "create an effort", "new effort", "start tracking",
  or when a workflow produces a project that should be tracked as an effort.
  Do NOT auto-invoke — only use when explicitly requested or when another skill delegates effort creation.
---

# Create Effort

This skill creates effort notes in the Obsidian vault. An effort is a trackable project or initiative with a goal, tasks, categories, and topics.

## What an effort looks like

Efforts live in the vault root (`/`) and have this structure:

```markdown
---
tags:
  - effort
categories:
  - "[[Category Name]]"
topics:
  - "[[Topic Name]]"
up: "[[]]"
year: "[[2026]]"
start:
end:
status: "[[Not started efforts]]"
created: 2026-03-06
modified: 2026-03-06
cssclasses: wide
---
> [!effort] Goal of this effort
> A concise description of what this effort aims to achieve.

## Links

- Link

## Tasks

- [ ] First task
- [ ] Second task

## Notes

![[Effort.base]]
```

## How to create an effort

### Step 1: Gather information

From the user's input, extract:
- **Title** — a clear, descriptive name for the effort (becomes the filename)
- **Goal** — what the effort aims to achieve (1–3 sentences)
- **Tasks** — initial set of actionable tasks
- **Categories and topics** — if provided in the prompt, use those

### Step 2: Suggest categories and topics

If the user didn't specify categories or topics, look up what exists in the vault:

```bash
Obsidian tags counts sort=count
Obsidian search query="categories" path=_meta limit=5
```

Also use your knowledge of the vault's existing notes to suggest relevant categories and topics. Categories are broad groupings (e.g., `[[AI projects]]`, `[[Portfolio]]`, `[[Home renovation]]`). Topics are thematic tags (e.g., `[[AI]]`, `[[Design process]]`, `[[Web development]]`).

Present your suggestions and ask the user to confirm or adjust before creating the file.

### Step 3: Write the effort file

Use the Write tool to create the file at the vault root. The filename is the effort title with `.md` extension.

Fill in all frontmatter fields:
- `tags`: always `effort`
- `categories`: wikilink format `"[[Category Name]]"`
- `topics`: wikilink format `"[[Topic Name]]"`
- `up`: leave as `"[[]]"`
- `year`: current year in wikilink format, e.g. `"[[2026]]"`
- `start`: leave empty (efforts are not started when created)
- `end`: leave empty
- `status`: `"[[Not started efforts]]"` unless the user specifies otherwise
- `created`: today's date
- `modified`: today's date
- `cssclasses`: `wide`

Write the goal inside the `> [!effort]` callout. Keep it concise but clear.

Add initial tasks as a markdown checklist under `## Tasks`. Group them under subheadings if there are natural categories. Use `- [ ] Task description` format.

Keep the `## Links` section with a placeholder `- Link` unless the user provided links.

Always end with `## Notes` containing `![[Effort.base]]`.

### Step 4: Open in Obsidian

After creating the file, open it in Obsidian:

```bash
Obsidian open file="Effort Title" newtab
```

If multiple efforts were created, open each one.

## Language

Write the effort content (goal, tasks) in the same language the user used in their request. Finnish input gets Finnish output, English input gets English output.

## When called by other skills

Other skills can delegate effort creation to this skill. The calling skill should provide at minimum:
- A title or subject for the effort
- Enough context to write a meaningful goal and initial tasks

When used as a utility, still confirm categories/topics with the user before creating unless the calling context makes the right choices obvious.
