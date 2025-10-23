---
mode: 'agent'
scope: 'workspace'
tools: ['edit', 'runNotebooks', 'search', 'new', 'runCommands', 'runTasks', 'usages', 'vscodeAPI', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo', 'extensions', 'todos', 'runTests']
description: 'Generate a concise, conventional commit message and exact git commands.'
---

Generate a concise, consistent commit message using repository context and diffs. Ensure varied commit types (not always "feat"), standardized emoji usage, and no advisory suggestions. The model must inspect diffs via `git --no-pager diff -- <file>`.

Inputs (provide where available)
- changed_files: list of files from `git status --porcelain`
- repo_context: short human-provided summary / intent (optional)
- diffs: (optional) per-file diff output; the model must run: `git --no-pager diff -- <file>` for each file to inspect details
- is_major_feature: boolean (true if a major feature is introduced)

Hard rules
1. Always prefer repo_context and code semantics first; use diffs only to confirm details. Do NOT rely solely on diffs.
2. For each changed file, the model MUST run: `git --no-pager diff -- <filename>` (or be provided that diff) and use it to produce concise summaries.
3. Do NOT include advisory suggestions like "recommend quick smoke", "you should test", or other prescriptive QA language in the commit message.
4. Commit subject must be imperative, present tense, ≤ 72 characters.
5. Use this emoji/type mapping (mandatory and consistent):
   - 🚀 feat: new features / major functionality
   - ⭐ enhancement: improvements / non-breaking enhancements
   - 🐛 fix: bug fixes
   - ♻️ refactor: refactors / code cleanup
   - 🧪 test: tests added/changed
   - 🔒 security: security fixes
   - 📝 docs: documentation only
   - 📦 chore: tooling, deps, formatting, build
6. Choose the most appropriate type for the change. Do NOT default to 🚀 feat for every commit — vary by actual change.
7. Header format:
   <emoji> <type>(optional-scope): Short summary
   - scope is optional (use module or filename when helpful)
8. Body structure (use sections only when relevant). Use unicode bullets (•) for sub-items:
   - Changes (<emoji/type>) — list per-file concise summaries:
     • <file/path> — one-line summary (focus on intent)
   - Features (🚀) — only if is_major_feature true; provide short impact statement
   - Notes / Breaking (⚠️) — only if relevant (explicitly state breaking changes)
9. Keep body short: aim for ≤ 24 lines. Use terse language.
10. If intent for a file is unclear, use "minor formatting/whitespace" or "unspecified code change" instead of guessing.
11. If a major feature is present (is_major_feature), add a Features section with a short description and impact.
12. Output must NOT contain testing recommendations or manual QA steps.
13. The commit message can have multiple sections like Fetures, changes, Notes, etc. It is even preffered if different types of changes like bug and features are there, seperate  section are produced.
15. Bug fixes takes higher priority than features.
16. Give the commit message command in codeblock as output like,
git commit -m "..."


Output format (exact)
Return a JSON-like block or plain text containing:
- file_count: <N>
- file_summaries:
  • <file1> — <one-line summary>
  • <file2> — <one-line summary>
  ...
- commit_message: (multi-line string using header + body per rules above)

Example inputs (for LLM reference)
- changed_files: ["utils.py", "postprocessors.py"]
- repo_context: "Make generated email HTML Outlook-friendly and adapt postprocessor headers"
- is_major_feature: true

Example commit messages (references)

Example A — New feature (use when major feature added)
🚀 feat(utils): make email HTML Outlook-compatible

Features (🚀)
• generate_html_page — rewrite signature/footer into presentation TDs for reliable Outlook rendering

Changes (⭐)
• utils.py — refactor HTML assembly; add MSO spacer and fix table insertion bug
• postprocessors.py — adapt CSV header names to new friendly labels

Notes / Breaking (⚠️)
• Generated email DOM changed; verify downstream HTML parsers consume the new structure

Example B — Bug fix
🐛 fix(utils): prevent extra table row in Outlook spacer

Changes (🐛)
• utils.py — replace table spacer with MSO-only paragraph + div fallback to avoid blank rows in old Outlook

Example C — Enhancement
⭐ enhancement(postprocessors): standardize CSV column names

Changes (⭐)
• postprocessors.py — normalize headers to "First Name" / "Last Name" and adjust decryption mapping

Usage guidance for the LLM
- Prefer repo_context for high-level intent, confirm details via `git --no-pager diff -- <file>`.
- Choose emoji/type based on overall change; vary types across commits.
- Produce concise one-line per-file summaries and one multi-line commit_message following the template.
- Do not include QA suggestions or instructions.