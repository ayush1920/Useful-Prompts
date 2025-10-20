---
mode: 'agent'
scope: 'workspace'
tools: ['githubRepo','search/codebase']
description: 'Create or update Markdown documentation; may create new files or update existing docs.'
---

# Prompt: docs (trigger: @docs)

When the user runs `@docs`, create or update documentation in Markdown. This prompt may inspect repository files to find existing docs to update.

Output requirements:
- If creating: suggest filename and front-matter (optional), provide an abstract (2 sentences), 3–5 section headings with 1–2 sentence content each, usage examples or commands, and a one-line changelog entry.
- If updating: produce a diff-style snippet showing the proposed edits to the named file.
- Keep content in Markdown only.

Rules:
- Ask clarifying questions only if no minimal description or selection is provided.
- Avoid introducing project-specific terminology unless present in the repository.

Parameters: `input` (selected text or feature description), `action` (create|update), `filename` (optional).

Example: `@docs action=create filename=HOWTO-deploy input="How to deploy the payments microservice"`
