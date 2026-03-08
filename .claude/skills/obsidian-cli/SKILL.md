---
name: obsidian-cli
description: >
  Interact with the Obsidian vault using the Obsidian CLI — search notes, query tags and
  properties, manage tasks, append to daily notes, create files from templates, query bases,
  check backlinks, and open files in the app. Use when performing any vault operation that
  benefits from structured queries rather than raw file access: searching, filtering by tags
  or properties, checking backlinks or orphans, querying bases, toggling tasks, manipulating
  frontmatter properties, or opening files in Obsidian. Other Obsidian skills should use this
  skill when they need to discover or query vault content before creating or editing notes.
  Direct file reads and writes still work for simple cases — the CLI adds structured querying,
  template-based creation, property manipulation, and app integration on top.
---

# Obsidian CLI

The Obsidian desktop app exposes a CLI via the `Obsidian` command (capital O). It queries and modifies the vault without touching files directly.

## When to use CLI vs. direct file access

**Use the CLI** for searching, filtering, querying structured data (tags, properties, bases, tasks, backlinks), creating files from templates, manipulating properties, and interacting with the app (opening files, running commands).

**Use direct file access** (Read, Edit, Write tools) for reading full note bodies, making precise line-level edits, or rewriting sections. The CLI's `read` command works but doesn't give you line numbers for editing.

The two complement each other. Typical flow: CLI to find and filter, then Read/Edit for the content.

## Syntax

```bash
Obsidian <command> [options]
```

- Default vault is `notebook`. For others: `vault=<name>`.
- `file=<name>` resolves like wikilinks. `path=<path>` is exact.
- Quote values with spaces: `name="My Note"`.
- Use `\n` for newline in content values.
- Many commands support `format=json|tsv|csv`. JSON is best for parsing.
- Most commands default to the active file when file/path is omitted.

## Common workflows

### Search the vault

```bash
Obsidian search query="design process"
Obsidian search query="AI agents" path=sources limit=10
Obsidian search:context query="skill-driven" format=json   # shows matching lines
Obsidian search query="vibe coding" total                   # just the count
```

### Query tags and properties

```bash
Obsidian tags counts sort=count                             # all tags by frequency
Obsidian tag name=internal verbose                          # files with a tag
Obsidian properties file="My Note" format=yaml              # all properties on a file
Obsidian property:read name=tags file="My Note"             # single property value
```

### Set and remove properties

Easier than parsing YAML frontmatter manually, especially for lists.

```bash
Obsidian property:set name=status value=done file="My Note"
Obsidian property:set name=tags value="[internal, ai]" type=list file="My Note"
Obsidian property:set name=rating value=4.5 type=number file="My Note"
Obsidian property:remove name=status file="My Note"
```

### Daily notes

```bash
Obsidian daily:read                                         # read today's note
Obsidian daily:path                                         # get the file path
Obsidian daily:append content="- Meeting at 3pm"            # add to end
Obsidian daily:prepend content="## Morning priorities"      # add to top
Obsidian daily                                              # open in Obsidian
```

### Create files

Template-based creation runs Obsidian's template engine (date variables, etc.) — a key advantage over writing files directly.

```bash
Obsidian create name="My Insight" template="🗒️ Internal"
Obsidian create path="sources/New Source.md" template="📖 Source"
Obsidian create name="Quick Note" content="Some content"
```

### Append and prepend

```bash
Obsidian append file="My Note" content="New paragraph"
Obsidian prepend file="My Note" content="Added to top"
```

### Check links and connections

```bash
Obsidian backlinks file="Design" format=json                # what links TO this
Obsidian links file="Design"                                # what it links OUT to
Obsidian orphans                                            # no incoming links
Obsidian deadends                                           # no outgoing links
Obsidian unresolved verbose                                 # broken links
```

### Tasks

```bash
Obsidian tasks todo                                         # incomplete tasks
Obsidian tasks file="Tasks" verbose                         # tasks in a file
Obsidian tasks daily                                        # tasks in daily note
Obsidian task path="Tasks.md" line=15 toggle                # toggle completion
Obsidian task file="Tasks" line=15 done                     # mark done
```

### Query bases

```bash
Obsidian bases                                              # list all base files
Obsidian base:query file="Sources" view="All" format=json   # query a view
Obsidian base:create file="Inbox" name="New Item"           # create item in base
```

### File operations

Obsidian updates all wikilinks when renaming/moving — use CLI over manual mv/rename.

```bash
Obsidian rename file="Old Name" name="New Name"
Obsidian move file="My Note" to=sources
Obsidian delete file="My Note"                              # moves to trash
```

### Open in Obsidian

```bash
Obsidian open file="My Note" newtab
Obsidian search:open query="design"
```

## Full command reference

For less common commands (plugins, sync, history, themes, bookmarks, snippets, hotkeys, dev tools), read `references/commands.md` in this skill's directory.
