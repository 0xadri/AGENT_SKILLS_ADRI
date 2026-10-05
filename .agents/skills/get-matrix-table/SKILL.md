---
name: get-matrix-table
description: create matrix table to analyze codebase or docs with the goal of answering a specific question - i.e. route auth audit, notification coverage, privacy leak scan. User provides problem description, goal, and relevant files/dirs. Trigger on "matrix table", "comparison table", "audit table", "inventory table", "which ones", "go through all".
---

# Guidelines

- Output goes to chat only. Never write files.
- Grounding is mandatory. Every row must cite the source file path, plus line numbers when useful. No row without evidence.
- Be exhaustive, not selective. One row per item found in scope. If an expected item is missing, keep the row and mark it `MISSING` with a comment.
- Keep cells short. Put nuance in the `Comment` column.
- No emojis. Use short text markers: `yes`, `no`, `n/a`, `WARN`, `MISSING`.
- When scope is large, several grouped tables (e.g. basic-user changes vs power-user changes) beat one giant unreadable table.

# Step 1 - Parse `$ARGUMENTS`

Extract three parts:

- `[PROBLEM]` - what to analyze (e.g. "all routes", "all emails we send")
- `[GOAL]` - what the table must reveal (e.g. "which require auth, which have security issues")
- `[SCOPE]` - files/dirs to search (e.g. `packages/backend/src/services`)

Rules:

- If `$ARGUMENTS` is empty or has no analyzable problem, ask for `[PROBLEM]` and `[GOAL]`. Stop until user responds.
- If `[SCOPE]` is missing or vague, do not guess blindly. Run a quick Glob/Grep using keywords from `[PROBLEM]` to infer candidate dirs, then ask one sharp question listing those candidates. Stop until user confirms.
- If `[PROBLEM]` and `[GOAL]` arrive fused in one sentence (usual case), split them yourself. No need to ask.

# Step 2 - Sweep the scope and collect evidence

- Search `[SCOPE]` with Grep/Glob, then Read the hits. Also check `packages/shared/` when the problem touches types, constants, or routes - both sides live there.
- Enumerate every item that becomes a row. Trace each item to its source and note `path:line`.
- Code is the source of truth. Always. Docs are helpers only - reading them first to find candidates faster is fine, but rows cite code, never the doc.
- When the problem is about artifacts like skills, the artifact files themselves are the code.

# Step 3 - Design the matrix

Derive columns from `[GOAL]`, plus always include:

- an `Item` column (route, email, event, feature, ...)
- an `Evidence` column (file refs)
- a `Comment` column for nuance and for calling out gaps (e.g. "no notification emitted - deliberate, trip creation is silent")

Verdict columns answer `[GOAL]` directly: `Auth?`, `Security risk?`, `Has HTML copy?`, `Emits notification?` - one column per question in the goal.

# Step 4 - Output the table

- Markdown table in chat. Short cell values, details in `Comment`.
- After the table, add a short `Findings` list: gaps, risks, surprises worth acting on. 9 bullets max.
- Split into grouped sub-tables when the table gets wide (see Guidelines).

# Example invocations

Arguments shape: `problem - goal - scope`.

```text
/get-matrix-table all routes - which require auth, which have security issues - packages/backend/src/routes packages/backend/src/middleware

/get-matrix-table all emails we send - html copy and text copy per email - packages/backend/src/services

/get-matrix-table emails leaking other party's phone number - when do we send it out - packages/backend/src/services
```

# Example output

For `/get-matrix-table all routes - which require auth, which have security issues - <routes dir> <middleware dir>`:

| Item                  | Auth? | Security risk? | Evidence                        | Comment                                                             |
| --------------------- | ----- | -------------- | ------------------------------- | ------------------------------------------------------------------- |
| GET /api/items        | no    | WARN           | `src/routes/item.routes.ts:120` | Public by design - search/browse                                    |
| GET /api/items/:id    | no    | WARN           | `src/routes/item.routes.ts:51`  | Public read. Returns item regardless of status, deleted included    |
| POST /api/items       | yes   | no             | `src/routes/item.routes.ts:162` | Auth middleware + schema validation. Ownership from token, not body |
| PUT /api/items/:id    | yes   | no             | `src/routes/item.routes.ts:218` | Ownership check in controller via ForbiddenError                    |
| DELETE /api/items/:id | yes   | no             | `src/routes/item.routes.ts:259` | Same pattern as PUT                                                 |
| POST /api/auth/login  | no    | no             | `src/routes/auth.routes.ts:22`  | Rate-limited entry point, must stay public                          |

Findings:

- GET endpoints expose soft-deleted items without auth - confirm acceptable
- DELETE is hard delete, no soft-delete trail
- All mutating routes follow auth -> validation -> handler, no drift
- Rate limiting is global (app-level), not per-route - worth a look for sensitive endpoints
