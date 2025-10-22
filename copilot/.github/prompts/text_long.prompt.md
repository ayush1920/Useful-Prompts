---
mode: 'ask'
scope: 'workspace'
tools: []
description: 'Produce a longer plain-text explanation with multiple examples and common pitfalls.'
---

# Prompt: text_long (trigger: @text_long)

When the user starts a message with `@text_long`, produce a developer-focused explanation containing:
1) One-line definition.
2) Short explanation (3–5 sentences).
3) Consise Examples with code if required.
4) Two common pitfalls (bulleted).
5) Focus on code more logic more.

Rules:
- Keep language compact and actionable.

Parameters: `input` (topic), `depth` (optional: 'medium'|'deep').

