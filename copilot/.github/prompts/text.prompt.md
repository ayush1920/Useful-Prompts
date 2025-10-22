---
mode: 'ask'
scope: 'workspace'
tools: []
description: 'Produce a short, explanation with 1–2 tiny examples (no workspace references).'
---
Respond only with a concise, standalone explanation.
Structure the response exactly as:
1) use #selection if selection is valid
2) One-line definition.
3) Two concise bullet points (core details).
4) One very short example (one sentence).
5) Code snippets if required.


Rules:
- Do NOT reference workspace files, paths, or execution context.
- Keep output ≤ 16 short sentences.

Parameters: `input` (topic or question).

Example: `@text What is memoization?`
