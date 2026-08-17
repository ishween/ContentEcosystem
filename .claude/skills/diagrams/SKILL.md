---
name: diagrams
description: Identify every concept in a script that needs a visual, catalog the diagrams I already made, and generate native Excalidraw files for the missing ones. Use when generating the diagrams artifact.
---

# Diagrams

Input: `scripts/<slug>.md`, `artifacts/<slug>/brief.md`, and any files I placed in
`artifacts/<slug>/diagrams/source/`
Output:
- `artifacts/<slug>/diagrams/manifest.md`
- `artifacts/<slug>/diagrams/generated/` (one file per new diagram)

No mermaid. Every generated diagram is a real `.excalidraw` file that opens directly
in the Excalidraw app or web, not a code block or a text description someone still
has to draw. Text descriptions are a fallback only, used sparingly (see step 3).

## Steps

1. Read the script and list every point that would be clearer with a visual. For
   each, note the beat marker (📌) it maps to.
2. Check `diagrams/source/` for diagrams I have already made. Mark those as
   "mine, done" in the manifest; do not regenerate them.
3. For each remaining visual, write `diagrams/generated/<slug-of-concept>.excalidraw`:
   a real Excalidraw file using the element schema below. Use simple, readable
   auto-layout: left-to-right or top-to-bottom flow of boxes connected by arrows,
   labeled text, generous spacing (150-250px gaps) so nothing overlaps. If a diagram
   is genuinely too intricate for reliable auto-layout (dense multi-branch trees,
   precise pixel arrangement, anything with more than roughly 8-10 connected shapes),
   skip the `.excalidraw` file and instead write
   `diagrams/generated/<slug-of-concept>.md` with an exact shape-by-shape,
   label-by-label, connection-by-connection description, precise enough to assemble
   in Excalidraw in a couple of minutes. This is the exception, not the default.
4. Write `manifest.md` as a table: concept, beat marker, status (mine / generated /
   generated-as-text-spec), file. This manifest is what the newsletter skill reads,
   so keep the concept names short and reference-able (for example "evals pipeline",
   not a full sentence).

## Excalidraw file format

A valid file is JSON shaped like this:

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "file://",
  "elements": [ /* element objects, see below */ ],
  "appState": { "gridSize": null, "viewBackgroundColor": "#ffffff" },
  "files": {}
}
```

Every element needs these common fields. Use the defaults shown unless a field is
called out as something you set per element:

```json
{
  "id": "<unique-string, e.g. box-1>",
  "type": "rectangle | ellipse | diamond | text | arrow",
  "x": 0, "y": 0, "width": 160, "height": 80,
  "angle": 0,
  "strokeColor": "#1e1e1e",
  "backgroundColor": "transparent",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "frameId": null,
  "roundness": { "type": 3 },
  "seed": 1234567,
  "version": 1,
  "versionNonce": 1234567,
  "isDeleted": false,
  "boundElements": null,
  "updated": 1700000000000,
  "link": null,
  "locked": false
}
```

Notes per element type:

- **Shape** (`rectangle`, `ellipse`, `diamond`): set `x, y, width, height`. If a text
  label lives inside it, add a `boundElements` entry
  `[{ "type": "text", "id": "<text-id>" }]`.
- **Text**: same common fields, plus `text`, `originalText` (same value), `fontSize`
  (20 for labels, 24-28 for a diagram title), `fontFamily: 1`, `textAlign: "center"`,
  `verticalAlign: "middle"`, `baseline: 18`, `lineHeight: 1.25`, and `containerId`
  set to the shape's `id` if it is bound inside a shape, else `null`. Position
  bound text roughly centered inside its container.
- **Arrow**: `points` is an array of `[x, y]` pairs relative to the arrow's own
  `x, y`, e.g. `[[0, 0], [200, 0]]` for a straight rightward arrow. To connect shapes
  properly (so Excalidraw treats it as a real connector, not a floating line), set
  `startBinding: { "elementId": "<from-id>", "gap": 8, "focus": 0 }` and
  `endBinding: { "elementId": "<to-id>", "gap": 8, "focus": 0 }`, and set
  `endArrowhead: "arrow"`, `startArrowhead: null`.
- `seed` and `versionNonce` just need to be distinct-ish integers per element; any
  values are fine, they do not need to be truly random.
- `id` values must be unique within the file. Prefix them by diagram so nothing
  collides across files, e.g. `evals-box-1`, `evals-arrow-1`.

Keep each diagram focused on one concept. If a script needs five visuals, that is
five separate `.excalidraw` files, not one crowded canvas.
