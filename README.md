# Riley Thinking Skills

Thinking, reflection, and general-purpose Claude skills — the ones that don't belong in
[riley-pm-skills](https://github.com/rileytrottier23/Riley-PM-Skills) (product work) or
[riley-coding-skills](https://github.com/rileytrottier23/Riley-Coding-Skills) (engineering). Versioned
here rather than left in a chat history.

Each skill is a folder containing a `SKILL.md`: an instruction set Claude loads when the skill's
description matches what you are asking for. They work in Claude Projects, Claude Code, and Cowork.

**This repo is also a plugin marketplace** — 20 skills installable in one step. See [Install](#install).

## Layout: `mine/` vs `vendored/`

- **[`mine/`](./mine)** — skills I wrote. MIT ([LICENSE](./LICENSE)).
- **[`vendored/`](./vendored)** — skills by other people, pinned to an upstream commit and kept under
  their original license. Nothing in here is my work; each folder credits its author.

The same split is used across all three skill repos, so "who wrote this and under what license" is
answerable at a glance.

## Install

```
/plugin marketplace add rileytrottier23/riley-thinking-skills
/plugin install riley-thinking-skills@riley-thinking-skills
```

**Claude desktop app / Cowork:** Customize → Plugins → Personal plugins → **+** → Add marketplace →
Add from a repository → `rileytrottier23/riley-thinking-skills`

Four plugins, install whichever you want:

| Plugin | Skills | What's in it |
|---|---|---|
| `riley-thinking-skills` | 10 | My thinking, reflection, personal-finance, chess, French, quality-check, integration-debugging, and skill-publishing skills |
| `anthropic-example-skills` | 8 | Anthropic's example skills — writing, comms, and creative (Apache 2.0) |
| `writing-skills-obra` | 1 | Strunk's Elements of Style, packaged by Jesse Vincent (public domain) |
| `avoid-ai-writing` | 1 | Conor Bronsdon's AI-writing pattern audit and rewrite (MIT) |

## My skills (`mine/`)

| Skill | What it does |
|---|---|
| [decision-partner](./mine/decision-partner) | A sharp, skeptical questioner for a decision you're facing — asks the hard questions instead of handing you an answer. |
| [decision-review](./mine/decision-review) | Reviews a decision you already made, separating the quality of the reasoning from the quality of the outcome. |
| [practice-partner](./mine/practice-partner) | Turns "I want to get better at X" into a deliberate-practice plan and holds you to it at check-ins. |
| [reflection-partner](./mine/reflection-partner) | Runs your weekly/monthly/annual reflection with pointed questions about where time and attention actually went. |
| [canadian-financial-modeler](./mine/canadian-financial-modeler) | Canada-specific personal-finance modelling — mortgages, TFSA/RRSP/RESP/FHSA, RSU tax, HELOC, rental analysis. |
| [chess-coach](./mine/chess-coach) | Practical chess coaching for the 700–1200 Elo range — tactics, openings, endgames, game analysis. |
| [french-tutor](./mine/french-tutor) | French practice with an emphasis on Quebec French, for an anglophone parent in a bilingual household. |
| [publish-skill-to-github](./mine/publish-skill-to-github) | Routes any new or edited skill to the right one of the three skill repos, into `mine/` or `vendored/`, and updates that repo's README, marketplace, and changelog. |
| [checker-agent](./mine/checker-agent) | Reviews any work artifact for accuracy, gaps, and risks before it ships. |
| [diagnose-broken-integration](./mine/diagnose-broken-integration) | Diagnoses automations, syncs, CI jobs, and credentials that broke or are failing silently. |

## Vendored skills (`vendored/`)

| Collection | Author | Skills | License |
|---|---|---|---|
| [anthropic](./vendored/anthropic) | [Anthropic](https://github.com/anthropics/skills) | 8 | Apache 2.0 |
| [obra-elements-of-style](./vendored/obra-elements-of-style) | [Jesse Vincent](https://github.com/obra/the-elements-of-style) | 1 | Public domain |
| [conorbronsdon-avoid-ai-writing](./vendored/conorbronsdon-avoid-ai-writing) | [Conor Bronsdon](https://github.com/conorbronsdon/avoid-ai-writing) | 1 | MIT |

Each vendored folder is a pinned snapshot, not a live mirror. Updating means a fresh vendor commit
against a newer upstream SHA — never an edit in place — so the diff always shows what changed upstream.

## Using them without the marketplace

Every skill is still a plain folder. Copy the whole directory into your `skills/` directory for Claude
Code, or zip it and upload it under Customize → Skills. Claude triggers it from the description in its
frontmatter — you don't need to invoke it by name.

## License

MIT — see [LICENSE](./LICENSE). Applies to [`mine/`](./mine) only. [`vendored/`](./vendored) keeps each
upstream author's own license, which governs.
