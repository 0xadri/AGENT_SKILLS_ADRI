---
name: sddj-frontmatter
description: Add/Update YAML frontmatter to doc files with metadata fields like status, version, tags
---

Process each file listed in: $ARGUMENTS

For each file, only add/update frontmatter, do not modify anything else in the file.

If there is something important, related to the frontmatter, that you think I should change. Warn me in the chat.

**Add/Update Frontmatter with fields:**
last_updated,
first_created,
author (ai-generated with <LLM name>),
generated_by (Adri),
status_doc,
granularity,
urgency,
importance,
risk_level,
priority,
risks,
tags (as an inline YAML array: [tag1, tag2, ...]),
related_docs (as an array of objects with path property)
related_code (as an array of objects with path property)

**Examples Of Possible Values (non exclusive)**

last_updated: "2026-01-27"
first_created: "2026-01-26"
author: "ai-generated with Sonnet" | "ai-generated with GPT-5.4" | "ai-generated with Gemini 3.1 Pro"
Never use tool/editor/agent names here. Wrong: "ai-generated with Opencode", "ai-generated with Claude Code", "ai-generated with Copilot", "ai-generated with Cursor".
Always use model/LLM name actually responsible for draft.
generated_by: "Adri"
status_doc: "draft" | "reviewed" | "completed"
granularity: "epic" | "feature" | "story" | "task"
urgency: "immediate" | "soon" | "eventually"
importance: "nice-to-have" | "should-have" | "must-have"
risk_level: "critical" | "high" | "medium" | "low"
priority: "top" | "high" | "medium" | "low"
risks: ["breaking-change", "regression", "security-sensitive", "data-migration", "experimental", "stable"]
tags: ["authentication", "api", "security", "docs", "frontend", "backend", "trip", "booking"]
related_docs:

- path: "docs/related.md"

related_code:

- path: "packages/frontend/src/pages/Trip/Trip.tsx"

Note that "priority" is a rough estimation combining the values of: granularity, urgency, importance, and risk_level. For instance, if the values are story, immediate, must-have, low; then priority must be set to "top".

For "status_doc", prompt the user to choose one of the statuses allowed.

**Dates**

For last_updated and first_created, use git to find out when was the file first created and last updated
