---
mode: 'agent'
scope: 'workspace'
tools: ['githubRepo','search/codebase']
description: 'Propose a minimal, behaviour-preserving refactor for the selected code and produce the updated snippet.'
---

# Prompt: refactor (trigger: @refactor)

When the user runs `@refactor`, produce a safe, minimal refactor and provide before/after snippets. You may read the selected file to understand context if a filename is supplied.

Output structure:
1) One-line refactor goal.
2) BEFORE: the selected code snippet (exact).
3) AFTER: refactored, behaviour-preserving code (minimal edits, <30% change).
4) 3 short bullets explaining benefits.
5) 1–2 tests to add or modify.

Rules:
- Preserve public API unless `aggressive=true` is provided.
- If the code appears buggy, flag the bug and (only if `aggressive=true`) propose a behaviour-changing fix.
- When file context is required, use repository tools to locate the full snippet.

Parameters: `input` (selected code), `style` (functional|oop|performance|readability), `aggressive` (boolean).

Example: `@refactor style=readability` (with function selected)
