---
name: visual-output
description: Shape replies so they scan instead of read — answer first, task list for shared work, tables, status scorecards, decisions set apart, diagrams for flows. Use for any reply to Riley that has more than one part: status updates, recaps, handoffs, comparisons, research answers, plans, and any task with 3+ steps that he and Claude work on together (in the Claude app or Claude Code). Skip only for one-line answers.
---

# Visual Output

Riley finds text-heavy replies hard to decipher. This skill sets the layout of a reply; `better-writing`
still governs the wording. It applies in the Claude app and in Claude Code alike.

## Why these rules

| Rule | Basis |
|---|---|
| Answer first | Readers scan the start of a reply and skip much of the rest |
| Small chunks | Working memory holds about four items at once |
| Tables and diagrams | Words plus pictures are easier to understand than words alone, and a side-by-side layout beats comparing from memory |
| Symbols for status | Colour and shape are noticed before words are read |
| Few options | More choices slow a decision down |

## 1. Task list for shared work

Whenever Riley and Claude work on a task together that has 3 or more steps, create a task list with the
surface's task tool (TaskCreate / TaskUpdate, or TodoWrite) **before the first real step**. Tick items off
as they finish. The last item is checking the work. Don't skip it because the task "seems quick".

## 2. Reply shape

1. **First line: the answer, result, or the one thing Riley must do.** Never open with a recap.
2. **Body: five bullets or fewer per group, three sections at most.** Past one screen, offer an artifact
   (Claude app) or a file (Code) instead.
3. **Recaps: 3 bullets or a 3-row table at most.** Don't retell the steps; the task list shows them.
4. **IDs, hashes, commands and paths go in code blocks**, never mid-sentence. Put them at the bottom
   unless Riley needs to run them now.
5. **Never write "see above".** If Riley has to act, put the full action (prompt, command, link) in this
   reply.

## 3. Pick the format

| Content | Format |
|---|---|
| Comparing 3+ items or options | Table |
| Status or progress | Scorecard (below) |
| Steps Riley will follow | Numbered list |
| A process, flow, or system with 4+ parts | Diagram |
| Numbers over time or across groups | Chart |
| Something he'll return to or share | Artifact |
| Everything else | Short prose |

**Diagrams are on by default** for flows, architectures, and sequences of 4+ steps. In the Claude app,
use the inline visual tool. In Code, use a Mermaid or small text diagram in a code block, or an HTML
artifact if it's large. Skip diagrams for simple lists; they cost more tokens than they save there.

## 4. Status scorecard

Use for any "where are we" reply, end-of-task summary, or handoff:

| | Item | Note |
|---|---|---|
| ✅ | Done thing | one line |
| ⚠️ | Blocked thing | why, one line |
| 👉 | Needs Riley | exactly what he does |

## 5. Decisions

When Riley has to choose, end the reply with a separate block:

**Your call:** a one-line question, then 2–3 numbered or lettered options, with the recommended one
first and marked. Never bury a decision inside a paragraph or a notes section.

## Before sending

- Does the first line tell Riley what he needs?
- Could any paragraph be a table, a list, or a diagram?
- Is anything he must act on only "above"?
