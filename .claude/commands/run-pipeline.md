---
description: Run the full content pipeline on a script, generating every artifact in parallel waves.
---

Read the target script (named as an argument, for example `scripts/rag-evals.md`) and
derive its slug from the filename.

1. **Wave 0** (do this yourself, do not spawn a subagent): create `artifacts/<slug>/`
   and its subfolders (`diagrams/source/`, `diagrams/generated/`, `linkedin/`). Read
   the script and write `artifacts/<slug>/brief.md` with the core problem, audience,
   key takeaways, case studies, and data points.

2. **Wave 1**: spawn three subagents using the Task tool. Dispatch all three Task
   calls in one message so they run in parallel, not one after another:
   - one runs the `teleprompter` skill
   - one runs the `diagrams` skill
   - one runs `youtube-meta` pass 1 only

3. **Wave 2**: once the diagrams manifest exists, spawn two subagents in parallel
   (again, both Task calls in the same message):
   - one runs the `newsletter` skill
   - one runs the `launch-post` skill

4. **Wave 3**: once the newsletter's Takeaways section exists, spawn one subagent for
   the `linkedin-posts` skill.

5. Print a summary: every artifact path created, plus anything a subagent flagged as
   needing my input (ambiguous lines, missing case studies, a diagrams manifest that
   was not found, etc).

Rules:
- Each subagent writes only its own files, per the skill's Output section. Never let
  two subagents write the same file.
- Do not run `youtube-meta` pass 2 (post-recording). That only runs when explicitly
  triggered after recording.
- If "run everything fully parallel" is said instead, skip the wave dependencies:
  spawn all six generation subagents (teleprompter, diagrams, youtube-meta pass 1,
  newsletter, launch-post, linkedin-posts) in one batch, and tell the newsletter and
  linkedin-posts agents to derive from `brief.md` directly instead of waiting on the
  diagrams manifest or each other. Flag in the summary that this run skipped
  cross-referencing for speed.
