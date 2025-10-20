---
mode: 'ask'
scope: 'workspace'
tools: []
description: 'Produce a short, plain-text explanation with 1–2 tiny examples (no workspace references).'
---

# Prompt: text (trigger: @text)

When the user starts a message with `@text`, respond only with a concise, standalone plain-text explanation.
Structure the response exactly as:
1) One-line definition.
2) Two concise bullet points (core details).
3) One very short example (one sentence).

Rules:
- Do NOT reference workspace files, paths, or execution context.
- Keep output ≤ 6 short sentences and avoid code blocks.

Parameters: `input` (topic or question).

Example: `@text What is memoization?`
