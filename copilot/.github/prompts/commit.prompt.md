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
2. **CHANGELOG PRIORITY**: If changed_files includes new/modified files in `changelogs/v*.*.*` (CHANGELOG.md, IMPLEMENTATION_SUMMARY.md, etc.), read those files directly instead of running diffs. Changelogs contain comprehensive change descriptions that are more accurate than diffs. Extract version, changes, features, and impact from the changelog content.
3. For non-changelog files, the model MUST run: `git --no-pager diff -- <filename>` (or be provided that diff) and use it to produce concise summaries.
4. Do NOT include advisory suggestions like "recommend quick smoke", "you should test", or other prescriptive QA language in the commit message.
5. Commit subject must be imperative, present tense, ≤ 72 characters.
6. **CHANGELOG-DRIVEN COMMITS**: When a version changelog is present (e.g., `changelogs/v2.1.0/CHANGELOG.md`), extract the version number and use it in the commit scope. Example: `feat(v2.1.0): add MongoDB pooling and async worker`. Summarize the changelog's Summary section for the header.
7. Use this emoji/type mapping (mandatory and consistent):
   - 🚀 feat: new features / major functionality
   - ⭐ enhancement: improvements / non-breaking enhancements
   - 🐛 fix: bug fixes
   - ♻️ refactor: refactors / code cleanup
   - 🧪 test: tests added/changed
   - 🔒 security: security fixes
   - 📝 docs: documentation only
   - 📦 chore: tooling, deps, formatting, build
8. Choose the most appropriate type for the change. Do NOT default to 🚀 feat for every commit — vary by actual change.
9. Header format:
   <emoji> <type>(optional-scope): Short summary
   - scope is optional (use module or filename when helpful)
   - For changelog commits, scope should be the version (e.g., `feat(v2.1.0): ...`)
10. Body structure (use sections only when relevant). Use unicode bullets (•) for sub-items:
   - **For changelog commits**: Structure body using the changelog's organization:
     • Extract from changelog: Summary, Features, Changes, Bug Fixes, Breaking Changes
     • Map changelog sections to commit sections (keep emoji/type consistent)
     • Preserve key technical details (version bumps, performance metrics, breaking changes)
   - **For regular commits**: Use standard structure:
     • Changes (<emoji/type>) — list per-file concise summaries (focus on intent)
     • Features (🚀) — only if is_major_feature true; provide short impact statement
     • Notes / Breaking (⚠️) — only if relevant (explicitly state breaking changes)
11. Keep body short: aim for ≤ 24 lines. Use terse language.
12. If intent for a file is unclear, use "minor formatting/whitespace" or "unspecified code change" instead of guessing.
13. If a major feature is present (is_major_feature), add a Features section with a short description and impact.
14. Output must NOT contain testing recommendations or manual QA steps.
15. The commit message can have multiple sections like Features, Changes, Bug Fixes, Notes, etc. It is even preferred if different types of changes like bug and features are there, separate sections are produced.
16. Bug fixes take higher priority than features in header selection.
17. **CHANGELOG WORKFLOW**: When detecting changelog files:
    a. Identify the version from the path (e.g., `changelogs/v2.1.0/CHANGELOG.md` → v2.1.0)
    b. Read the CHANGELOG.md file content directly (do NOT use git diff)
    c. Extract: version, bump type (MAJOR/MINOR/PATCH), summary, features, changes, bug fixes, breaking changes
    d. Use the changelog's summary section for commit header
    e. Organize commit body mirroring changelog structure (Features → Changes → Bug Fixes → Breaking)
    f. Include version.txt changes if present (e.g., "• changelogs/version.txt — bumped to 2.1.0")
18. Give the commit message command in codeblock as output like:
```bash
git commit -m "..."
```


Output format (exact)
Return a JSON-like block or plain text containing:
- file_count: <N>
- file_summaries:
  • <file1> — <one-line summary>
  • <file2> — <one-line summary>
  ...
- commit_message: (multi-line string using header + body per rules above)

Example inputs (for LLM reference)

**Example Input 1 — Regular commit**
- changed_files: ["utils.py", "postprocessors.py"]
- repo_context: "Make generated email HTML Outlook-friendly and adapt postprocessor headers"
- is_major_feature: true

**Example Input 2 — Changelog commit**
- changed_files: ["changelogs/v2.1.0/CHANGELOG.md", "changelogs/v2.1.0/IMPLEMENTATION_SUMMARY.md", "changelogs/version.txt", "scheduler/db_actions.py", "scheduler/scheduler.py", "scheduler/job_queue.py"]
- repo_context: "Version 2.1.0 release with MongoDB pooling, increased parallelism, and async worker pattern"
- is_major_feature: true

Example commit messages (references)

**Example A — Regular feature commit**
```bash
git commit -m "🚀 feat(utils): make email HTML Outlook-compatible

Features (🚀)
• generate_html_page — rewrite signature/footer into presentation TDs for reliable Outlook rendering

Changes (⭐)
• utils.py — refactor HTML assembly; add MSO spacer and fix table insertion bug
• postprocessors.py — adapt CSV header names to new friendly labels

Notes / Breaking (⚠️)
• Generated email DOM changed; verify downstream HTML parsers consume the new structure"
```

**Example D — Changelog-driven commit**
```bash
git commit -m "🚀 feat(v2.1.0): add MongoDB pooling, async worker, and 67% parallelism boost

Features (🚀)
• MongoDB connection pooling — 10-50 connections with auto-retry prevents connection loss under load
• Async worker pattern — Redis-ready job queue with FIFO processing and semaphore limiting
• Increased parallelism — semaphore 6→10 concurrent executions (+67% throughput)

Changes (⭐)
• changelogs/v2.1.0/CHANGELOG.md — comprehensive feature documentation with migration guide
• changelogs/v2.1.0/IMPLEMENTATION_SUMMARY.md — detailed technical analysis and deployment plan
• changelogs/version.txt — bumped to 2.1.0
• scheduler/db_actions.py — added connection pooling with retryWrites/retryReads
• scheduler/scheduler.py — increased semaphore limit from 6 to 10
• scheduler/job_queue.py — implemented async worker methods (set_job_executor, start_worker, stop_worker)

Bug Fixes (🐛)
• Race condition resolved — atomic queue operations prevent duplicate job executions

Notes / Breaking (⚠️)
• MongoDB 3.6+ required for retryWrites support
• New indexes needed: job_pipelines (pipeline_id+scheduled_for, pipeline_id+status, scheduled_for)
• Async worker methods added but not yet integrated (foundation for future Redis migration)"
```

**Example B — Bug fix commit**
```bash
git commit -m "🐛 fix(utils): prevent extra table row in Outlook spacer

Changes (🐛)
• utils.py — replace table spacer with MSO-only paragraph + div fallback to avoid blank rows in old Outlook"
```

**Example C — Enhancement commit**
```bash
git commit -m "⭐ enhancement(postprocessors): standardize CSV column names

Changes (⭐)
• postprocessors.py — normalize headers to \"First Name\" / \"Last Name\" and adjust decryption mapping"
```

Usage guidance for the LLM
- **STEP 1 - Detect changelogs**: Check if `changelogs/v*.*.*` files are in changed_files. If yes, proceed to Step 2. If no, proceed to Step 3.
- **STEP 2 - Changelog-driven commit**:
  * Read changelog files directly (CHANGELOG.md, IMPLEMENTATION_SUMMARY.md, version.txt)
  * Extract version number from path (e.g., `v2.1.0`)
  * Use changelog Summary section for commit header
  * Map changelog sections to commit body: Features (🚀) → Changes (⭐/♻️) → Bug Fixes (🐛) → Breaking (⚠️)
  * For other modified files (non-changelog), use diffs to create concise summaries
  * Commit scope should be the version (e.g., `feat(v2.1.0): ...`)
- **STEP 3 - Regular commit** (no changelog):
  * Use repo_context for high-level intent
  * Run `git --no-pager diff -- <file>` for each changed file
  * Create concise one-line per-file summaries
  * Choose emoji/type based on overall change; vary types across commits
- **ALWAYS**: Do not include QA suggestions or instructions.
- **OUTPUT**: Produce one multi-line commit_message following the template, wrapped in bash codeblock.