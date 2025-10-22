---
mode: 'agent'
scope: 'workspace'
tools: ['search/codebase', 'runCommands', 'fetch', 'githubRepo']
description: 'Create or update architecture docs with mermaid diagrams embedded in Markdown.'
---
When invoked, produce architecture documentation that includes self-contained mermaid diagrams and explanatory sections. This prompt may read repository code to infer components and interactions when a filename or selection is provided.

Output structure:
1) Suggested filename and a short abstract.
2) One or two mermaid diagrams (flowchart and component graph) in fenced code blocks.
3) Sections: components, data flows, failure modes (each 2–4 short bullets).
4) A concise deployment and scaling recommendation.

Rules:
- Diagrams must be valid mermaid syntax and self-contained.
- If repository context is available, prefer real component names; otherwise ask the user for component names.

Parameters: `input` (system description or selected doc), `action` (create|update), `filename` (optional).

Example: `@architecture action=create filename=ARCH-payment-system input="payments, auth, db, queue"`
