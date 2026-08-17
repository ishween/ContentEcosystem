---
name: newsletter
description: Write the Substack/Medium newsletter version of a script using the What, Why, How, Takeaways structure. Use when generating the newsletter artifact.
---

# Newsletter (Substack / Medium)

Input: `scripts/<slug>.md`, `artifacts/<slug>/brief.md`, `artifacts/<slug>/diagrams/manifest.md`
Output: `artifacts/<slug>/newsletter.md`

Voice: warm, conversational, big-sister. Spell out contractions. No em-dashes.

Structure the piece exactly like this:

## What
- State the problem or topic in one or two plain sentences.
- Add a placeholder line: `Watch the video: <YOUTUBE_LINK>` for listeners.

## Why
- Who is this for?
- Why should they read it? Name the concrete benefit.

## How / Topics
- Walk through the topics with explanations.
- Where the diagrams manifest maps a visual to a beat, reference it inline, for
  example "See diagram: evals pipeline". Do not invent diagrams that are not in the
  manifest, and do not skip referencing one that is.

## Takeaways
- Summarize the key takeaways as a short list.
- End with clear next steps for the reader.

If the diagrams manifest is not present yet when this skill runs, note that in a
one-line flag at the top of the file (`<!-- diagrams manifest not found, references
omitted -->`) rather than blocking or inventing diagram names.
