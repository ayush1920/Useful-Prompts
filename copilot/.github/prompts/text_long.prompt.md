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
3) Three concise examples (1 sentence each).
4) Two common pitfalls (bulleted).
5) Two recommended next-steps or readings (titles only).

Rules:
- Do NOT reference workspace files or execution environment.
- Keep language compact and actionable.

Parameters: `input` (topic), `depth` (optional: 'medium'|'deep').

Example: `@text_long Explain the circuit-breaker pattern`
