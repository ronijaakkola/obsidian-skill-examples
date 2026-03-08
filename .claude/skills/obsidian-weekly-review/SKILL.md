---
name: obsidian-weekly-review
description: >
  Guide the user through a structured weekly review and planning session for their Obsidian notebook.
  Use when the user says "weekly review", "review my week", "plan next week",
  "let's do a weekly review", "what did I do this week",
  or any variation of wanting to reflect on the past week and plan the next one. Also trigger when
  the user asks to fill in their weekly note's evaluation section, set next week's goals, or do
  their regular end-of-week reflection routine. This is an interactive interview-style session, not
  a one-shot summary.
---

# Weekly Review & Planning

Guide the user through a structured weekly review and planning session. This is an interactive,
interview-style conversation with three phases: summary, review, and planning.

## Language and tone

Conduct the interview in the same language the user uses. When recording the user's answers to
their weekly notes, preserve their exact words. You may clean up formatting (add bullet points,
fix spacing) but never rephrase, translate, or editorialize their thoughts.

## Asking questions

Ask interview questions as regular chat messages — just output the question as text. The user
will respond in their next message. Ask one question at a time and wait for the response before
proceeding. Do not use structured prompts or selection tools — the point is open-ended
reflection, and the user needs space to write freely.

## Before starting

Determine which week to review. The user typically does the review on **Sunday evening or
Monday morning**, so the review covers the week that just ended.

To figure out the correct week:

1. Calculate the current ISO week from today's date.
2. Read the current week's note (`planning/{current_week}.md`) and the previous week's note.
3. Check which one has **unfilled focus areas** (placeholders like `*Write here*`) and
   which one has actual content. The week with filled-in focus areas but an empty evaluation
   section is the one to review.
4. If it's ambiguous (e.g., both are empty or both are filled), ask the user which week they
   want to review.

Once you know the target week, calculate:
- **Review week**: the ISO week being reviewed (e.g., `2026-W09`)
- **Previous week**: the week before that (e.g., `2026-W08`)
- **Next week**: the week after the review week (e.g., `2026-W10`)

## Phase 1: Summary

Gather data and present a summary before starting the interview. Use the `obsidian-cli` skill
for vault queries.

### Calculate the week's date range first

Before querying tasks, calculate the exact Monday–Sunday date range for the review week.
ISO weeks start on Monday and end on Sunday. For example:
- **2026-W09** = Monday 2026-02-23 through Sunday **2026-03-01** (note: Sunday can be in
  a different month than Monday)

Store these as `week_start` (Monday) and `week_end` (Sunday). Use these for all date
filtering below. A task date matches the week if: `week_start <= date <= week_end`.

### Data to gather

1. **Tasks completed this week** — run `Obsidian tasks done format=json` to get all completed
   tasks. The CLI returns every completed task in the vault, so you need to filter the results.
   Dates are embedded in the task text using Obsidian Tasks emoji format:
   ```
   - [x] Task description 📅 2026-03-01 ✅ 2026-02-28
   ```
   The `✅ YYYY-MM-DD` is the completion date. Extract it from each task's text and keep
   only tasks where `week_start <= completion_date <= week_end`.

2. **Incomplete tasks due/scheduled this week** — run `Obsidian tasks todo format=json` to
   get all incomplete tasks. Extract date markers from the text field:
   - `📅 YYYY-MM-DD` = due date
   - `⏳ YYYY-MM-DD` = scheduled date
   Keep only tasks where the due date OR scheduled date falls within `week_start` to
   `week_end` (inclusive). Discard tasks whose dates are outside this range — even by one day.

   Tasks live across many files — `Tasks.md`, effort notes, memory notes, daily notes, etc.
   The CLI searches them all.

3. **Internal notes created this week** — find notes tagged `#internal` with a `created`
   property falling in the review week. The CLI doesn't support property-range queries
   directly, so use this approach:
   - Get all files tagged `#internal` via `Obsidian tag name=internal format=json`
   - Read the `created` property of recent files to filter to the review week
   - Alternatively, use `Glob` on root-level `*.md` files sorted by modification time and
     check frontmatter of recent ones

   These notes represent the user's own thinking during the week.

4. **Memories from the week** — check `memories/` for files with date prefixes matching the
   review week (filenames start with `YYYY-MM-DD`). Use `Glob` with a pattern like
   `memories/2026-02-2*.md` for the relevant date range. These are personal journal entries.

5. **Previous week's evaluation** — read the previous week's note
   (`planning/{prev_week}.md`) and extract `## Weekly evaluation`, especially
   `### ➡️ Do next`. If the previous week's evaluation is empty (the sections
   have no content below them), note this and move on — the user sometimes skips weeks, and
   that's fine.

6. **Current week's focus areas** — read the review week's note and check whether
   `## Focus areas` and `## This week is a success if..` were filled in.

### Present the summary

Structure it as:

**Week YYYY-WNN summary**

- **Completed tasks** — list completed tasks (or note if none were completed)
- **Incomplete tasks** — list incomplete tasks that were scheduled
- **Thoughts this week** — list internal notes created, with a brief thematic summary (1–2
  sentences capturing themes, not a note-by-note recap)
- **Memories and events** — mention notable memories, or note that none were recorded
- **Previous week's goals** — what did the previous week's "➡️ Do next"
  say, and did the user follow through? If the previous week had no evaluation, skip this.

If the review week had focus areas set, also note:
- **Week's focus** — the 3 focus items and whether the success criteria appears to have been met

After presenting, ask: "Here's your week summary. Want to comment on anything before we move
to the review?"

## Phase 2: Review (interview)

The review is recorded in the **review week's** weekly note under `## Weekly evaluation`.

### Compare with previous week

Before the first question, briefly highlight the connection to the previous week's
"➡️ Do next" if it had content. For example: "Last week you decided to [X].
How did that show up this week?"

If the previous week's evaluation was empty or the note doesn't exist, skip this comparison
entirely and move straight to the questions.

### Interview questions

Ask these one at a time as a chat message. Wait for the response before asking the next.

1. **➕ What worked**
   Ask: "What worked this week? What are you happy about?"
   If the user is brief, gently probe: did any focus areas work well? Was there a task or
   thought from the summary worth highlighting?

2. **➖ What didn't work**
   Ask: "What didn't work? What was frustrating or difficult?"
   If relevant, reference incomplete tasks or unmet commitments from the summary — but don't
   push. Let the user lead.

3. **➡️ Do next**
   Ask: "What will you do differently or continue next week?"
   This naturally bridges into the planning phase.

### Record the review

After all three answers, write them to the review week's note. Use the `Edit` tool to fill in
the sections under `## Weekly evaluation`:

- Under `### ➕ What worked` — add a bullet list of what worked
- Under `### ➖ What didn't work` — add a bullet list of what didn't work
- Under `### ➡️ Do next` — add a bullet list of commitments

If the sections already have content, append rather than overwrite.

Confirm to the user that the review has been recorded.

## Phase 3: Planning (interview)

Planning is recorded in the **next week's** weekly note.

### Ensure the next week's note exists

Check if `planning/{next_week}.md` exists. If it doesn't, create it.

Try the obsidian-cli first:
```
Obsidian create path="planning/{next_week}.md" template="🗓️ Weekly"
```

If template processing doesn't resolve the Templater variables correctly (the file contains
raw `<% ... %>` expressions), overwrite the file with a manually resolved version. Here is
the exact format — replace all `{placeholders}` with calculated values:

```markdown
---
tags:
  - weekly
---
← [[{prev_week}]] | [[{next_next_week}]] →

## Focus areas

1. *Write here*
2. *Write here*
3. *Write here*

## This week is a success if..

*Write here*

## Tasks

> [!warning]- Overdue
> ```tasks
> not done
> tags do not include #scratch
> due before {monday_of_next_week}
> ```

> [!todo]+ This week
> ```tasks
> not done
> (due in {next_week}) OR (scheduled in {next_week})
> tags do not include #scratch
> group by due
> short mode
> ```

> [!success]- Completed
> ```tasks
> done
> done in {next_week}
> tags do not include #scratch
> group by due
> ```

## Weekly evaluation

### ➕ What worked

### ➖ What didn't work

### ➡️ Do next
```

Where:
- `{prev_week}` = the review week (e.g., `2026-W09`)
- `{next_next_week}` = two weeks ahead (e.g., `2026-W11`)
- `{monday_of_next_week}` = Monday of next week in `YYYY-MM-DD` format
- `{next_week}` = the week being planned (e.g., `2026-W10`)

### Bridge from review

Before asking planning questions, summarize what came up in the review — especially the
"➡️ Do next" items. Prompt the user: "In your review, you mentioned [X].
Do you want to make this a focus for next week?"

Also mention if you noticed any patterns from the week's notes or recurring themes that
might be worth focusing on.

### Interview questions

Ask one at a time as a chat message:

1. **Focus areas (3 items)**
   Ask: "What are the three most important focus areas for next week?"
   If the user needs help, suggest items based on: review commitments, incomplete tasks,
   themes from their internal notes and memories. But let the user decide.

2. **Success criteria**
   Ask: "When is next week a success? What's a concrete measure?"
   Encourage something specific rather than vague.

### Suggestions

The user plans their own tasks and schedule — don't generate a task list. However, if you
notice something actionable from the week's data (a pattern, a repeated struggle, an open
thread in their notes or memories), mention it as a suggestion. Frame it conversationally:
"I noticed in your notes that [X]. Would you like to add something related to your tasks?"

### Record the plan

Write to the next week's note using the `Edit` tool:

- Under `## Focus areas` — replace the three placeholder lines (`*Write here*`) with
  the user's focus areas as a numbered list
- Under `## This week is a success if..` — replace the placeholder with the success
  criteria

Confirm that the plan has been recorded.

## Wrapping up

After all three phases, offer to open the notes in Obsidian:
```
Obsidian open file="{review_week}"
Obsidian open file="{next_week}" newtab
```

End with a brief encouraging note. Keep it natural, not formulaic.
