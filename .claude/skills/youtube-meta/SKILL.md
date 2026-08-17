---
name: youtube-meta
description: Generate YouTube titles, tags, thumbnail overlay text, and the timestamped description for a video. Runs in two passes, before and after recording. Use when generating the youtube-meta artifact.
---

# YouTube metadata

Input: `artifacts/<slug>/brief.md` and `scripts/<slug>.md`
Output: `artifacts/<slug>/youtube-meta.md`

This skill runs in two passes. Only run pass 2 when explicitly told recording is
done; never run it automatically as part of `/run-pipeline`.

## Pass 1, pre-recording (runs during the pipeline)

- **Title options**: 3 options, written around search and demand words people
  actually type. Note the target keyword under each.
- **Tags**: a set aimed at reaching the right audience.
- **Thumbnail overlay text**: 3 short options, a few words each, high contrast
  phrasing.

Write these into `youtube-meta.md`, then add a clearly marked section:
`## Description and timestamps (fill after recording)`.

## Pass 2, post-recording (runs only when told recording is done)

- Fill the description: a short hook paragraph, then the timestamp list with a title
  per section.
- I will provide the actual timestamps, or point to a file with them. Do not invent
  timestamps. If none are available yet, leave the section marked
  `<!-- awaiting timestamps -->` rather than guessing.

## Example (structure, not content)

```
Title options:
1. How do we evaluate RAG pipelines with LLMs? (keyword: RAG evaluation)
2. ...
3. ...

Tags: llm evaluation, rag pipeline, ai interview question, ...

Thumbnail overlay text:
1. "Evals or it didn't happen"
2. ...
3. ...

## Description and timestamps (fill after recording)
This is a real interview question from a big tech company...

00:00 Question: Reliability with LLMs
01:00 Observability and evals
02:50 What are evals?
06:41 Who does evals?
08:11 Answer relevancy, groundedness, tool sequence
10:39 Bloopers
```
