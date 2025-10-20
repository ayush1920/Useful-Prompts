---
mode: 'agent'
scope: 'workspace'
tools: ['githubRepo','search/codebase']
description: 'Generate unit/integration tests for selected code or a referenced file.'
---

# Prompt: tests (trigger: @tests)

When invoked as `@tests`, generate a complete test file ready to paste into the repository. This prompt **may** read the repository (via tools) to find referenced code when a filename is provided.

Output requirements:
- Produce a full test file for the selected function/module with 4–8 test cases covering typical behaviour and edge cases.
- Include at least one negative/failure test asserting exceptions or invalid inputs.
- If external IO/HTTP/DB is used, include one example using mocking/stubbing (e.g., monkeypatch, jest.mock).
- Provide a one-line command to run the tests (e.g., `pytest tests/test_module.py`).

Rules:
- Do not invent API signatures not present in the selection or in the referenced file; if selection is absent, ask for a short description.
- Aim for readable, deterministic tests that require minimal external setup.

Parameters: `input` (selected code or filename), `framework` (pytest|jest|mocha|unittest), `target` (python|javascript).

Example: `@tests framework=pytest target=python` (with function selected)
