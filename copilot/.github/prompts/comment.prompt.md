---
mode: 'agent'
scope: 'workspace'
tools: ['githubRepo','search/codebase']
description: 'Add concise, information-dense comments to selected code or add a short header to a file.'
---

# Prompt: comment (trigger: @comment)

When invoked, produce the same code with short inline comments (<=1 line each). If no selection is provided, produce a 3–4 line file header summarizing purpose, public API, and invariants. This prompt may modify or suggest edits to files and thus can access repo context.

Rules:
- Do not change code behaviour; only add comments.
- Comments should be precise, avoid filler, and include complexity notes, units, or representative example values where helpful.
- If the file is large, annotate only the selected region or public APIs by default.

Parameters: `input` (selected code or filename), `style` (inline|header-only).

Example: `@comment input=<selected function> style=inline`
