# Content Pipeline

This repo turns one video script into every downstream content artifact:
teleprompter copy, diagrams, newsletter, launch post, LinkedIn posts, and YouTube
metadata. A team of subagents generates these in parallel, each following its own
skill in `.claude/skills/`. See PLAN.md for the full design.

## What lives where

- Input scripts: `scripts/<slug>.md`
- Generated artifacts: `artifacts/<slug>/`
- My published edits (source of truth for the feedback loop): `feedback/published/<slug>/`
- Skill improvement history: `feedback/changelog.md`

## House style (non-negotiable, applies to all generated text)

- Spell out all contractions. Write "I am" not "I'm", "we are" not "we're",
  "do not" not "don't", "it is" not "it's", "you are" not "you're".
- Never use em-dashes. Use commas, colons, or separate sentences instead.
- Voice for newsletter and LinkedIn: warm, conversational, big-sister tone. Direct,
  encouraging, plain language. No corporate filler.
- Keep the reader oriented: lead with the problem or the payoff, not with throat
  clearing.
- Preserve beat markers written as the pin emoji (📌) in the source script. They mark
  where a diagram or on-screen moment lands. Never drop them, never invent new ones.

## How to run

- Generate everything for a script: `/run-pipeline scripts/<slug>.md`
- After I have recorded and know the timestamps: tell Claude to run the YouTube
  post-recording pass, pointing it at the timestamp source.
- Propose skill improvements from my published edits: `/reflect`

## Orchestration rules (read before running the pipeline)

- The pipeline runs in waves (see PLAN.md). Everything inside one wave must be
  dispatched as Task calls in a single message so they truly run in parallel.
  Dispatching them one at a time defeats the point of the wave structure.
- Every subagent writes only its own distinct file or folder under
  `artifacts/<slug>/`. Never let two subagents write the same file.
- Do not run the YouTube post-recording pass as part of `/run-pipeline`. It is a
  separate, deferred trigger.

## Feedback loop (pattern-gated auto-apply)

`/reflect` diffs `feedback/published/<slug>/` against `artifacts/<slug>/` for every
slug that has published edits. When the same edit pattern (a tone shift, a structural
change, a word consistently added or cut, a section reordered or removed) shows up
across **two or more scripts** for the same skill, `/reflect` applies the
corresponding edit to that skill's `SKILL.md` directly and appends one line to
`feedback/changelog.md` describing the change and the evidence for it. A pattern seen
in only one script is logged as a note but not applied, since a single edit is noise.

This means skills genuinely improve as I publish more edited content, without me
approving every change by hand, while still requiring repeated evidence before a
skill file is touched. Every change lands as its own git commit so it stays
reviewable and revertible after the fact.
