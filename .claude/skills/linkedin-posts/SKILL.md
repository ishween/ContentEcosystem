---
name: linkedin-posts
description: Generate multiple LinkedIn posts from one script across four distinct hook angles. Use when generating the LinkedIn artifacts.
---

# LinkedIn posts

Input: `artifacts/<slug>/brief.md` and `artifacts/<slug>/newsletter.md`
Output: one file per post under `artifacts/<slug>/linkedin/`, named by angle and
number.

Voice: warm, conversational, big-sister. Spell out contractions. No em-dashes. Each
post stands alone and ends with a light invitation to watch the video.

Generate posts across all four angles:

1. **Data-driven** (3 posts): each opens with a data point pulled from the script,
   for example a striking number or statistic ("we have 34K emotions..." style hook).
   Files: `data-driven-1.md` to `data-driven-3.md`.

2. **Case study** (one post per case study in the script): open with the production
   failure or the reason it mattered in production, then the lesson. Files:
   `case-study-1.md` and so on, one per case study found. If the script has no case
   study, skip this angle and note the skip in the pipeline summary rather than
   inventing one.

3. **Problem to solution** (3 posts): state the problem the video solves, then the
   solution in one line or as a key takeaway, including the production example where
   relevant. Files: `problem-solution-1.md` to `problem-solution-3.md`.

4. **FOMO takeaways** (one post per takeaway): the biggest takeaway becomes the hook.
   Produce one post for each distinct takeaway in the brief. Files: `fomo-1.md` and
   so on.

At the top of each file, add a one-line comment noting the angle and the hook used,
so I can scan them quickly, for example:

```
<!-- angle: data-driven | hook: "we have 34K emotions labeled in this dataset" -->
```
