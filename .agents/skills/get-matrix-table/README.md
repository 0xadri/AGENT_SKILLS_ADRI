# Readme For Humans

This guide is for humans only, to help you dear human being.

# Why Use This Skill?

Answer "which ones" questions with evidence, not vibes:

- which routes require auth, which have security issues
- which emails we send, which have HTML copy
- which features emit notifications, which are silent
- and so on

Benefits:

- exhaustive inventory, one row per item found in scope, nothing skipped
- every row grounded with file ref (`path:line`), code is source of truth
- verdict columns derived from your goal, nuance in `Comment`
- closes with a short `Findings` list: gaps, risks, surprises worth acting on

# FAQ: Matrix Table Skill

- What? -> Creates a matrix table analyzing codebase or docs to answer a specific question.

- When? -> Whenever you want to go through all items of a kind and see which ones satisfy what. Trigger on "matrix table", "comparison table", "audit table", "inventory table", "which ones", "go through all".

- Will it do any code change? -> No. Never. Output goes to chat only.

## Example Prompt

Run skill along with `problem - goal - scope` such as:

```markdown
/get-matrix-table all routes - which require auth, which have security issues - packages/backend/src/routes packages/backend/src/middleware

/get-matrix-table all emails we send - html copy and text copy per email - packages/backend/src/services

/get-matrix-table emails leaking other party's phone number - when do we send it out - packages/backend/src/services
```

## Example Output

For `/get-matrix-table all routes - which require auth, which have security issues - packages/backend/src/routes packages/backend/src/middleware`:

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
