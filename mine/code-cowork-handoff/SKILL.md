---
name: code-cowork-handoff
description: Use in Claude Code or Cowork when some or all of the current work fits the other surface better; flags the switch and writes a paste-ready handoff .md for the other side.
---

# Code ↔ Cowork Handoff

This skill runs on both surfaces. Its job is to notice when work belongs on the other surface, say so clearly, and write a handoff file the user can drop into the other side so it picks up the work without re-explaining.

## 1. Figure out where you are

- **Claude Code**: you are in a terminal or IDE session, usually inside a git repo, with direct shell access to the project.
- **Cowork**: you are in the Claude app with things like artifacts, Claude Docs, connectors (Gmail, Calendar, Drive, Notion), the browser, scheduled tasks, or remote-device tools.

If you can't tell, assume the surface whose tools you actually have.

## 2. Check for a handoff (do this without being asked)

Check when a task starts, when the plan changes, and when a new chunk of work comes up. Only hand off a meaningful chunk. Don't hand off a one-line fix or a quick question the current side can handle fine.

### In Claude Code, shift to Cowork when the work is mainly:

- Email, calendar, or messages (drafting, sending, scheduling, triage)
- Documents, decks, spreadsheets, or reports that people will read, not code
- Research and synthesis (competitive analysis, market scans, reading many web pages)
- Work in connected apps: Drive, Notion, Gmail, Readwise, and similar
- Browser tasks in the user's logged-in accounts
- Dashboards or pages to publish and share as artifacts
- Recurring or scheduled work (daily briefings, reminders, check-ins)
- Organizing personal files that aren't in a repo
- Stakeholder communication: updates, briefs, PRDs, FDDs

### In Cowork, shift to Claude Code when the work is mainly:

- Changing code across several files in a real repo
- Running a build, tests, a linter, or a local dev server over and over
- Debugging that needs stack traces, logs, or trying things out
- Git work: branches, commits, rebases, PRs, resolving conflicts
- Refactors, dependency upgrades, migrations, or scaffolding a project
- Deploy or infrastructure changes driven from the codebase (CI config, Dockerfiles, Railway config)
- Anything where the repo's own conventions (CLAUDE.md, tests) should steer the work

### Mixed tasks

Split them. Do the part that fits here, and hand off only the part that fits the other side. Say which is which.

## 3. When a handoff applies

1. Finish, or cleanly pause, the part that belongs here. Don't leave anything half-written or broken.
2. Write the handoff file right away (template below). Don't ask first.
3. Tell the user in one short, separate line:
   **Handoff → [Claude Code | Cowork]: [one-sentence reason]. File: `[name]`.**
4. If the user says to keep going here instead, respect that and continue.

## 4. Where the file goes

- **Name:** `HANDOFF-to-[code|cowork]-[short-slug]-[YYYY-MM-DD].md`
- **From Claude Code:** save it in `./handoffs/` at the repo root (create the folder if needed). If the repo has a `.gitignore`, suggest adding `handoffs/` but don't change it yourself. Print the full path.
- **From Cowork:** if a folder is connected, save it there (the project's repo folder if it's connected). Also deliver it in the chat so the user can copy it. If no folder is connected, deliver it in the chat only.

## 5. Handoff file template

The file must make sense on its own. The receiving side has none of this conversation. Use real paths, links, names, and values. Never write "as discussed."

```markdown
# Handoff: [task title]

**From:** [Claude Code | Cowork] → **To:** [Cowork | Claude Code]
**Date:** YYYY-MM-DD
**Why the switch:** [one sentence]

## Paste this to start
> [A ready-to-send first message for the receiving side. It names the goal and
> asks it to read the rest of this file before doing anything.]

## Goal
[What done looks like, in 1 to 3 sentences.]

## Context
- Project / repo: [name, path or URL, branch]
- Key files, docs, or links: [list]
- People involved: [names and roles, if relevant]

## Done so far
- [what was finished on this side, with file paths or links]

## Decisions already made
- [decision] because [reason]. Don't reopen unless asked.

## Tasks for the other side
1. [ ] [specific task]
2. [ ] [specific task]

## Constraints
- [deadlines, style rules, things not to touch, secrets to avoid]

## How to verify
- [tests to run, what the output should look like, who reviews it]

## Open questions for [user's name]
- [anything only the user can decide. Leave it empty if there's nothing.]

## Hand back when
[What should make the receiving side write a handoff back, for example: "Once the
PR is merged, hand back to Cowork to write the stakeholder update."]
```

## 6. Receiving a handoff

If the user pastes or points to a `HANDOFF-to-...md` file:

1. Read it all before acting.
2. Restate the goal and the first step in one or two lines.
3. Treat the decisions as settled, and ask about anything under Open questions.
4. Work through the task list. When you hit the "Hand back when" condition, write a handoff back using this skill.

## Rules

- Keep handoffs short. Aim for one screen and lead with the paste-ready prompt.
- Never put secrets, tokens, or passwords in a handoff file. Name where they're kept instead.
- Don't hand off just to avoid work the current side can do well.
- One handoff per chunk of work. If the plan changes, update the existing file instead of making a new one.
