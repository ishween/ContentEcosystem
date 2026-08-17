---
name: teleprompter
description: Turn a raw video script into a clean plain-text teleprompter file for reading aloud on camera. Use when generating the teleprompter artifact from scripts/<slug>.md.
---

# Teleprompter

Input: `scripts/<slug>.md` and `artifacts/<slug>/brief.md`
Output: `artifacts/<slug>/teleprompter.md`

Only process the main script body: everything from the cold open through the close.
Stop before a trailing `Vertical lift — <label>` block if present; that is a separate
short-form spinoff handled by the `vertical-lift` skill, not this one.

Produce a clean reading script:

- Keep only the spoken words. Strip the top metadata line (`Runtime target: ...`,
  `Thumbnail template: ...`) and section headers entirely, they are not spoken. If
  section headers (`Cold open`, `Section 1 — ...`, `Close`) are useful as visual
  breaks for the person reading off the teleprompter, keep them as a short bracketed
  divider, e.g. `[Section 1]`, clearly distinct from spoken lines, never narrated.
- Convert production cues into a short on-their-own-line marker, so I know where the
  moment lands, without cluttering the read with the full description:
  - `{excalidraw — <description>}` becomes `[diagram]` on its own line.
  - `{capture: <url>}` becomes `[capture]` on its own line.
  - `[cross-link: <label>]` is dropped entirely. It is not a visual beat, just a
    relationship between scripts, and has no place in a reading copy.
  Never drop a diagram or capture cue, never add one that was not in the source.
- Remove other production asides and meta commentary not meant to be read aloud
  (parentheticals describing delivery, camera notes, etc).
- Spell out all contractions and use no em-dashes (house style, see CLAUDE.md).
- Short lines and short paragraphs, easy to read at a glance. One idea per line where
  possible.
- Do not add new content or rephrase my meaning. This is a cleanup pass, not a
  rewrite. If a line is genuinely ambiguous (unclear where it ends, or whether an
  aside was meant to be read aloud), leave a `<!-- CHECK: ... -->` comment inline
  rather than guessing, and call it out in the pipeline summary.

## Example

Source line:
```
So here's the thing (pause for effect, maybe show my confused face) — evals aren't
optional anymore. {excalidraw — a funnel showing raw output narrowing to pass/fail}
they're the thing that tells you if your LLM app actually works.
```

Teleprompter output:
```
So here is the thing:
evals are not optional anymore.

[diagram]

They are the thing that tells you if your LLM app actually works.
```
