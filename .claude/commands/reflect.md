---
description: Compare my published edits against the generated artifacts, and auto-apply skill improvements once a pattern repeats across scripts.
---

For every slug that has a folder in `feedback/published/`, compare each published
file against the matching generated file in `artifacts/<slug>/`.

1. Diff them and identify recurring edit patterns: tone changes, structural changes,
   words consistently added or cut, sections reordered or removed. Do this per slug
   first, then group findings by which skill produced the artifact (teleprompter,
   diagrams, newsletter, launch-post, linkedin-posts, youtube-meta).

2. For each skill, check whether the same pattern appears across **two or more
   different scripts**:
   - **If yes**: this is a confirmed pattern. Edit that skill's `SKILL.md` directly
     to encode it (add or tighten an instruction, adjust the example, etc). Keep the
     edit minimal and targeted to the pattern, do not rewrite unrelated parts of the
     skill. Then append one line to `feedback/changelog.md`:
     `<date> - <skill> - <what changed> - evidence: <slugs>`.
     Commit this as its own git commit, message format:
     `reflect: update <skill> skill (pattern confirmed across <N> scripts)`.
   - **If a pattern shows up in only one script**: do not touch the skill. Write it
     as a dated note in `feedback/proposals/<date>/<skill>.md` instead, so the
     evidence is not lost. The next time `/reflect` runs and a second script confirms
     the same pattern, it graduates to an applied change automatically. Do not merge
     or apply these notes yourself.

3. If a published file is byte-for-byte identical to its generated counterpart,
   skip it, there is nothing to learn from an unedited artifact.

4. When finished, print a summary: which skills were updated and why, which patterns
   are on watch (single-script, not yet applied), and which slugs had no published
   counterpart yet to compare against.

Only ever touch a `SKILL.md` file when a pattern is confirmed by more than one
script. A single edit is noise, a repeated edit is a signal. Never touch
`CLAUDE.md`, the commands, or anything outside the matched skill file.
