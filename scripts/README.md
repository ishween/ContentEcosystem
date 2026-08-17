Drop one raw script per video here as `<slug>.md`. The slug (filename without
extension) is used as the folder name under `artifacts/` and `feedback/published/`.

## Conventions the skills key off of

- `{excalidraw — <description>}` marks where a diagram lands. The description is
  read directly as the brief for what to draw, so make it specific (e.g. "five
  boxes, each labelled with its store", not "a diagram here").
- `{capture: <url>}` marks where a screenshot of an external page lands, for
  something to grab rather than draw.
- `[cross-link: <label>]` marks a reference to another script/video in a series
  (e.g. `[cross-link: Part 2]`).
- A top-of-script metadata line works if formatted like
  `Runtime target: ~8 min Thumbnail template: 2 (diagram-hero, yellow card)`.
- An optional trailing `Vertical lift — <label>` block holds short-form
  (Reels/Shorts/TikTok) spinoff scripts, one idea per hook line, each with a target
  runtime in parentheses and a one-line rationale before the spoken script. Handled
  by the `vertical-lift` skill, separate from the main artifacts.

Everything above the `Vertical lift` block (or the whole file, if there is none) is
the main script, read by teleprompter, diagrams, newsletter, launch-post,
linkedin-posts, and youtube-meta.
