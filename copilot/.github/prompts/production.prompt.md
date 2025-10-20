---
mode: 'agent'
scope: 'workspace'
tools: ['githubRepo','search/codebase']
description: 'Convert a snippet into production-ready code: validation, error handling, logging, and minimal configuration.'
---

# Prompt: production (trigger: @production)

When invoked, transform the provided function/module into production-ready code. This prompt may read repository files to ensure compatibility and propose concrete changes.

Output must include:
- A production-ready code snippet implementing the feature (validation, error handling, logging, retries when appropriate).
- 3–6 bullets summarizing the changes made (validation, logging, env keys, retries).
- Required runtime/config keys (env var names and purpose).
- Backwards-compatibility notes and migration steps (if applicable).
- A short example invocation and one small test or usage snippet.

Rules:
- Avoid introducing heavy new dependencies; prefer standard libs or already-present project deps.
- Document any new dependency and justify it briefly.

Parameters: `input` (selected code or module), `language` (python|node|go), `strict` (boolean).

Example: `@production language=python strict=true`
