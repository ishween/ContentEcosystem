---
name: vertical-lift
description: Turn the trailing "Vertical lift" block of a script into standalone short-form (Reels/Shorts/TikTok) teleprompter-ready scripts. Use when generating the vertical-lift artifact, and only if the script has such a block.
---

# Vertical lift (shorts)

Input: `scripts/<slug>.md` (only the trailing `Vertical lift — <label>` block, not the
main script body), and `artifacts/<slug>/brief.md`
Output: `artifacts/<slug>/vertical-lift/<hook-slug>.md`, one file per short-form idea
found in the block

If the script has no trailing `Vertical lift — <label>` block, do nothing and say so
in the pipeline summary. Do not invent a short-form spinoff that was not written.

## Steps

1. Read only the block after the main script's close (everything under the
   `Vertical lift — <label>` header). The main script body is input to the
   teleprompter, diagrams, newsletter, launch-post, linkedin-posts, and youtube-meta
   skills, not this one; ignore it here.
2. The block usually contains one or more short-form ideas, each shaped like:
   a hook/title line, often with a target runtime in parentheses (e.g. "(40s)"), a
   one-line rationale for why it works as a standalone clip, then the spoken script
   itself. Split into one output file per idea if more than one is present.
3. For each idea, write a file with:
   - A short header: the hook line, the target runtime, and the one-line rationale,
     exactly as given, not rephrased.
   - The clean spoken script below it, formatted the same way the teleprompter skill
     formats a reading copy: short lines, one idea per line, contractions spelled
     out, no em-dashes, only the words meant to be read aloud.
4. Do not add ideas, extend the script, or pad the rationale. This is a cleanup and
   split pass on what was written, not a generator of new short-form content.

Naming: derive the filename from the idea's hook line, kebab-cased and trimmed, e.g.
"The fail-open question" -> `the-fail-open-question.md`.

## Example

Source block:
```
Vertical lift — Part 1
The fail-open question (40s). Strong, because it's a question rather than a fact,
and questions get comments.
Here's a question I ask in every agent design review, and about half the time
nobody has an answer. What does your agent do when the memory store is down?
```

Output file `the-fail-open-question.md`:
```
# The fail-open question
Target runtime: 40s
Why it works: strong, because it's a question rather than a fact, and questions get
comments.

---

Here is a question I ask in every agent design review,
and about half the time nobody has an answer.

What does your agent do when the memory store is down?
```
