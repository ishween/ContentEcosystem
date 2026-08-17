---
name: launch-post
description: Write the launch announcement post for a new video using the Title, one-line What, takeaways, hashtags format. Use when generating the launch-post artifact.
---

# Launch post

Input: `artifacts/<slug>/brief.md` and `artifacts/<slug>/newsletter.md`
Output: `artifacts/<slug>/launch-post.md`

Voice: warm, conversational. Spell out contractions. No em-dashes.

Produce, in this order:

- **Title**: one strong line.
- **What**: the topic in one line.
- **Takeaways / why**: two to four lines on what the viewer will learn and why it
  matters.
- **Hashtags**: a short, relevant set.

Model the shape on this example (structure, not content):

```
Title: How do we evaluate RAG pipelines with LLMs?

What: This is a real interview question from a big tech company, asked in a
technical round. The video explains the answer in roughly eleven minutes.

Takeaways: reliability with LLMs, observability and evals, what evals are, who runs
them, answer relevancy and groundedness.

Hashtags: #LLMs #AI #InterviewReady
```
