---
name: teleprompter
description: Turn a raw video script into a clean plain-text teleprompter file for reading aloud on camera. Use when generating the teleprompter artifact from scripts/<slug>.md.
---

# Teleprompter

Input: `scripts/<slug>.md` and `artifacts/<slug>/brief.md`
Output: `artifacts/<slug>/teleprompter.md`

Produce a clean reading script:

- Keep only the spoken words. Remove diagram notes, production asides, and any meta
  commentary that is not meant to be read aloud.
- Preserve beat markers (the pin emoji, 📌) on their own line where they appear, so I
  know where a diagram or on-screen moment lands. Never drop one, never add one that
  was not in the source.
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
optional anymore. 📌 [diagram: evals pipeline]
they're the thing that tells you if your LLM app actually works.
```

Teleprompter output:
```
So here is the thing:
evals are not optional anymore.

📌

They are the thing that tells you if your LLM app actually works.
```
