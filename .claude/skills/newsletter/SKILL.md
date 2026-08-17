---
name: newsletter
description: Write the newsletter version of a script, for Substack and LinkedIn Newsletter, using the What, Why, How, Takeaways structure. Use when generating the newsletter artifact.
---

# Newsletter (Substack / LinkedIn Newsletter)

Input: `scripts/<slug>.md`, `artifacts/<slug>/brief.md`, `artifacts/<slug>/diagrams/manifest.md`
Output: `artifacts/<slug>/newsletter.md`

Publishing targets: this single draft is meant to go out on **both Substack and
LinkedIn Newsletter** with no or minimal rework, not one platform. Write it plain
enough that either publish step is a copy-paste.

Voice: warm, conversational, big-sister. Spell out contractions. No em-dashes.

Structure the piece exactly like this:

## Headline
- One strong line, doubling as the Substack subject line and the LinkedIn Newsletter
  headline. Specific and benefit-forward, not vague ("How do we evaluate RAG
  pipelines with LLMs?", not "Thoughts on evals").

## What
- State the problem or topic in one or two plain sentences.
- Add a placeholder line: `Watch the video: <YOUTUBE_LINK>` for listeners. Right
  after it, add `<!-- LinkedIn: move this link to the first comment before
  publishing, external links in the body suppress reach there -->` so I do not
  forget when cross-posting.

## Why
- Who is this for?
- Why should they read it? Name the concrete benefit.

## How / Topics
- Walk through the topics with explanations.
- Where the diagrams manifest maps a visual to a script section, reference it inline,
  for example "See diagram: evals pipeline". Do not invent diagrams that are not in
  the manifest, and do not skip referencing one that is. A manifest row with status
  "capture" is a screenshot, not a diagram; reference it the same way but do not call
  it a diagram in the text (e.g. "see the AgentCore Memory blog post" rather than
  "see diagram").

## Takeaways
- Summarize the key takeaways as a short list.
- End with clear next steps for the reader.

If the diagrams manifest is not present yet when this skill runs, note that in a
one-line flag at the top of the file (`<!-- diagrams manifest not found, references
omitted -->`) rather than blocking or inventing diagram names.
