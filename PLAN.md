# Content Pipeline: Build Plan for Claude Code

## Goal

One script goes in. Every downstream artifact comes out, generated in parallel by a
team of subagents, each following its own skill. Edits I make to the published
artifacts feed back into the skills, so the system improves the more I use it.

## How to use this file

1. Open Claude Code in an empty git repository. (Done: this repo.)
2. Say: "Read PLAN.md and build the entire repo, including every SKILL.md and slash
   command exactly as specified." (Done.)
3. To run the pipeline later: drop a script at `scripts/<slug>.md`, then say
   "Run the pipeline on scripts/<slug>.md", or use `/run-pipeline scripts/<slug>.md`.
4. To improve the skills later: put my final published versions in
   `feedback/published/<slug>/`, then run `/reflect`.

---

## Repository structure

```
content-pipeline/
  CLAUDE.md                      # project context, loaded every session
  PLAN.md                        # this file
  .claude/
    skills/
      teleprompter/SKILL.md
      diagrams/SKILL.md
      newsletter/SKILL.md
      launch-post/SKILL.md
      linkedin-posts/SKILL.md
      youtube-meta/SKILL.md
    commands/
      run-pipeline.md            # /run-pipeline
      reflect.md                 # /reflect
  scripts/
    <slug>.md                    # input: one raw script per video
  artifacts/
    <slug>/                      # output: everything generated from that script
      brief.md
      teleprompter.md
      diagrams/
        manifest.md
        source/                  # my own generated diagrams go here
        generated/               # Claude-generated diagram specs
      newsletter.md
      launch-post.md
      linkedin/
        data-driven-1.md ...
        case-study-1.md ...
        problem-solution-1.md ...
        fomo-1.md ...
      youtube-meta.md
  feedback/
    published/
      <slug>/                    # my final edited versions, mirroring artifacts/
    proposals/                   # single-script edit notes that did not meet the
                                  # repeat-pattern bar (kept for context, not applied)
    changelog.md                 # running log of applied skill changes
```

Naming rule that prevents race conditions: every subagent writes to its own distinct
file or folder under `artifacts/<slug>/`. No two agents ever write the same file.

---

## House style (every artifact inherits this)

This lives in `CLAUDE.md` so every subagent and every skill follows it without being
told twice.

- Spell out all contractions. Write "I am" not "I'm", "we are" not "we're",
  "do not" not "don't", "it is" not "it's", "you are" not "you're".
- Never use em-dashes. Use commas, colons, or separate sentences instead.
- Voice for newsletter and LinkedIn: warm, conversational, big-sister tone. Direct,
  encouraging, plain language. No corporate filler.
- Keep the reader oriented: lead with the problem or the payoff, not with throat
  clearing.
- Preserve any beat markers written as the pin emoji (📌) in the source script. They
  mark where a diagram or on-screen moment lands.

---

## Orchestration: how the agents run in parallel

The lead agent (the main Claude Code session) reads the script once, then spawns
subagents with the Task tool. Each subagent runs in its own context, loads exactly
one skill, and writes exactly one artifact.

**Mechanism note:** Claude Code does not parallelize Task calls automatically. Every
wave below must be dispatched as multiple Task tool calls inside a *single* assistant
message. Sending them one at a time, waiting for each to finish, runs the wave
serially and defeats the point. `/run-pipeline` is written with this explicitly
called out. If the agent-teams research preview is enabled, that surface can be used
instead; the skills and wave structure do not change either way.

Full parallelism is not possible for every artifact, because some genuinely depend on
others. The pipeline runs in waves. Everything inside a wave runs at the same time.

- **Wave 0, setup** (lead agent, not a subagent): create `artifacts/<slug>/` and its
  subfolders. Read the script and extract a shared brief: the core problem, the
  target audience, the key takeaways, and any case studies or data points mentioned.
  Save this as `artifacts/<slug>/brief.md` so downstream agents do not each
  re-derive it.

- **Wave 1, parallel** (three subagents at once):
  - teleprompter skill  -> `teleprompter.md`
  - diagrams skill       -> `diagrams/manifest.md` and `diagrams/generated/`
  - youtube-meta skill   -> `youtube-meta.md` (pre-recording section only)

- **Wave 2, parallel** (two subagents at once, start after Wave 1 diagrams finish):
  - newsletter skill   -> `newsletter.md` (references the diagrams manifest)
  - launch-post skill  -> `launch-post.md`

- **Wave 3** (one subagent, starts after the newsletter takeaways exist):
  - linkedin-posts skill -> `linkedin/*.md` (one file per post across the four angles)

- **Deferred, second trigger** (run only after I have recorded and know the
  timestamps):
  - youtube-meta skill, post-recording pass -> fills the description and timestamps
    section of `youtube-meta.md`

Optional pure-parallel mode: if I say "run everything fully parallel", the newsletter
and linkedin agents may re-derive from the brief instead of waiting on the diagrams
and each other. This is faster but slightly less consistent across artifacts. Waves
are the default.

When the run finishes, the lead agent prints a short summary: which artifacts were
created, their paths, and anything a subagent flagged as needing my input.

---

## The skills

Each lives at `.claude/skills/<name>/SKILL.md`. See those files for the full
frontmatter and body; summarized here:

- **teleprompter**: raw script -> clean spoken-word reading copy, beat markers kept.
- **diagrams**: script -> manifest of every visual needed, cataloging mine vs.
  Claude-generated diagrams, produced as native `.excalidraw` files (openable
  directly in Excalidraw, no mermaid).
- **newsletter**: script + brief + diagrams manifest -> Substack/Medium post in
  What / Why / How / Takeaways structure.
- **launch-post**: brief + newsletter -> Title / What / Takeaways / Hashtags
  announcement post.
- **linkedin-posts**: brief + newsletter -> multiple posts across four angles
  (data-driven, case study, problem-to-solution, FOMO takeaways).
- **youtube-meta**: brief + script -> titles, tags, thumbnail text (pre-recording),
  then description and timestamps (post-recording, deferred).

---

## Slash commands

- **`/run-pipeline`**: runs Waves 0 through 3 on a named script, dispatching each
  wave's subagents in parallel. Never runs the YouTube post-recording pass.
- **`/reflect`**: diffs `feedback/published/<slug>/` against `artifacts/<slug>/`
  across every published slug, and applies pattern-gated skill updates. See below.

---

## Feedback loop, in plain terms

The generator writes a draft into `artifacts/`. I edit the version I actually publish
and drop it into `feedback/published/<slug>/`. When I run `/reflect`:

1. It diffs what it wrote against what I published, for every slug with published
   edits, and identifies patterns: tone changes, structural changes, words I
   consistently add or cut, sections I reorder or remove.
2. It groups patterns by which skill produced the artifact.
3. **If the same pattern shows up across two or more scripts for the same skill**, it
   applies the corresponding edit to that skill's `SKILL.md` directly, and appends one
   line to `feedback/changelog.md` naming the change, the skill, and the scripts that
   evidenced it.
4. **If a pattern shows up in only one script**, it is not applied. It is written as a
   dated note under `feedback/proposals/<date>/` so the evidence is not lost, and gets
   picked up automatically the next time `/reflect` runs if a second script confirms
   it.
5. Every applied change is its own git commit, so the whole history is auditable and
   revertible with `git revert` if a skill update turns out to be wrong.

This is a deliberate middle ground: skills improve on their own as I publish more
edited content, without me clicking approve on every diff, but a single one-off edit
can never permanently warp a skill on its own. It takes a repeated signal.

---

## Connectors and extensions worth wiring in

Nothing below is required for the pipeline to work; these are optional next steps.

- **Hostinger Reach** (already connected in this environment as an MCP server): a
  contacts/email tool. Once `newsletter.md` is finalized, a subagent could push it as
  a campaign or sync the audience segment directly, instead of copy-pasting into
  Substack. Worth wiring as a `/publish-newsletter` command later if newsletters go
  out through Reach rather than Substack natively.
- **YouTube Data API** (would need its own MCP connector or a small script): lets the
  post-recording `youtube-meta` pass pull real video duration and, once the video is
  uploaded, lets a follow-up step actually set the title/description/tags instead of
  just drafting them.
- **A transcription tool** (Descript, Otter, or local Whisper): if scripts start life
  as a rough spoken take rather than a written draft, a transcription step ahead of
  Wave 0 would produce `scripts/<slug>.md` automatically.
- **Excalidraw**: no MCP connector exists for it today, but no mermaid either. The
  `diagrams` skill writes native `.excalidraw` JSON files directly into
  `diagrams/generated/`, using the same element schema Excalidraw itself saves, so
  they open and are editable in the Excalidraw app or web with no re-drawing. My own
  `.excalidraw` exports go in `diagrams/source/` and get cataloged, not regenerated.
- **Notion or Airtable**: a lightweight content calendar keyed by slug, updated at
  the end of `/run-pipeline`, would give a single dashboard view across in-progress
  scripts instead of scanning `artifacts/`.
- **Buffer, Hootsuite, or Zapier**: for scheduling the LinkedIn posts once written,
  rather than posting by hand.

None of these are built into the skills below. Ask for any of them by name when
ready and they can be added as a new command or a new skill without touching the
existing six.

## Build order

Do these in sequence. Do not build the feedback loop first, because it needs a
history of my edits to learn from.

1. Create CLAUDE.md, the repo structure, and all six SKILL.md files. (Done.)
2. Create the `/run-pipeline` command. Test it on one real script end to end.
3. Tune the individual SKILL.md files until the artifacts come out close to what I
   want.
4. Add the `/reflect` command and the feedback folders. (Done, ready to use once
   published edits exist.)
5. After several real scripts have been published, run `/reflect` and let the
   pattern-gated updates start landing.

## Notes

- Mechanism: this plan uses Claude Code subagents spawned with the Task tool,
  dispatched in parallel batches per wave, which is the stable way to fan out work.
  The agent-teams research preview can be used instead if enabled; the skills and
  wave structure do not change either way.
- Keep everything in git from the first commit, especially the skills. The feedback
  loop depends on being able to diff, branch, and roll back.
