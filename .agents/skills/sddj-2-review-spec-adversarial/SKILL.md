---
name: sddj-2-review-spec-adversarial
description: Review spec adversarially — attack ambiguity, policy conflicts, rollout risks, and codebase drift before implementation. Use when user asks for adversarial spec review, hostile review, spec gate, or pre-implementation risk review.
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which spec file should I review adversarially? (e.g. `docs/plans/feature-name_spec.md`)"

Given spec at: $ARGUMENTS

Execute 7 steps below sequentially.

## Steps

### Step 1 — Pre-flight: open questions gate

Before any review work:

- Read the spec file at path provided in $ARGUMENTS.
- Locate the `## Open Questions` section. If the section is absent, empty, or states there are no open questions, proceed to Step 2.
- For each listed open question, an item counts as **decided** only if:
  - an explicit answer or decision is recorded next to it (e.g. `A:`, `Decision:`, `Resolved:`), or
  - it is explicitly marked as solved and the answer is stated in the rest of the spec (e.g. answered in `## Key Decisions`).
- If any open question is **undecided**, HALT immediately:
  - Do NOT run Steps 2-7. Do NOT output a review report.
  - List every undecided open question.
  - Tell the user to first address/resolve these open questions, then re-run the review.

### Step 2 — Load reference, repo rules, spec

- Read `.agents/skills/sddj-0-create-spec/SKILL.md` to understand expected spec structure and drafting rules.
- Read repo instructions from `AGENTS.md` and any repo-local constraints relevant to specs, architecture, auth/security, shared types, dependencies, and Prisma changes.
- Read spec file at path provided in $ARGUMENTS.

### Step 3 — Structural and internal consistency attack

Review spec as if engineer will implement it literally.

Check for:

- Missing required sections from spec template without justification.
- Incomplete sections, TODOs, placeholders, hand-wavy wording.
- Internal contradictions across sections.
- Ambiguity: places where two competent engineers could implement different behavior.
- Spec/plan leakage: implementation-task details that should be in a separate plan, not spec.
- False certainty: claims presented as settled facts without support.

Classify each issue as one of:

- **Critical** — likely wrong implementation, broken contract, unsafe change, or blocked implementation.
- **Major** — likely rework, confusion, drift, or under-specified behavior.
- **Minor** — useful clarification, but not likely to derail implementation alone.

### Step 4 — Hard-stop and policy scan

Explicitly inspect whether spec proposes or implies any repo hard-stop area.

Check for:

- shared type changes in `packages/shared/`
- `API_ROUTES` changes
- Zod schema contract changes
- Prisma schema changes or migrations
- auth/security changes
- new dependencies
- layer boundary violations (routes → controllers → services → repositories → database)

For each hard-stop area found:

- say whether spec acknowledges it or not
- say whether user confirmation would be required before implementation
- flag omission as issue if spec hides or understates impact

### Step 5 — Failure-mode and rollout attack

Look for missing or weak treatment of:

- invalid input and validation behavior
- authorization and permission edges
- retries, duplicate submissions, idempotency
- concurrency / race conditions
- partial success / rollback behavior
- stale data / old records / historical data compatibility
- migration, backfill, rollout, and deploy sequencing
- observability/logging needs when behavior can fail silently

Do not require every topic for every spec. Only flag missing items when feature risk or scope makes them necessary.

### Step 6 — Codebase verification

Use `Grep`, `Glob`, and `Read` to verify technical claims against actual codebase.

Verify at minimum:

- routes/endpoints mentioned
- Prisma models/fields/enums mentioned
- components/pages/hooks/services referenced
- shared types/constants referenced
- existing docs or archived specs used as references
- dependency claims such as "already installed", "no breaking changes", or "existing endpoint"

Flag each mismatch as:

- **Mismatch** — spec contradicts codebase
- **Stale reference** — path, filename, or reference doc is wrong or outdated
- **Unsupported claim** — spec states something as true but codebase does not prove it

### Step 7 — Present adversarial review report

Output review in this format:

```
## Adversarial Spec Review: [spec name]

### Summary
[1-3 sentences. State whether spec is safe to implement, risky, or not ready.]

### Critical Findings
- [issue] — [why this can ship wrong behavior / break policy / block implementation]

### Major Findings
- [issue] — [impact]

### Minor Findings
- [issue] — [impact]

### Hard-Stop / Confirmation Required
- [area] — [why explicit user confirmation is required before implementation]

### Codebase Mismatches
- [what spec says] vs [what codebase shows]

### Missing Failure Cases
- [missing case] — [why it matters here]

### Literal-Implementation Risks
- [if engineer follows this spec exactly, what likely goes wrong]

### Verdict

**>> [SELECTED VERDICT] <<**

Choose exactly one and replace line above with it:
- `>> SAFE TO IMPLEMENT <<`
- `>> REVISE BEFORE PLANNING <<`
- `>> NOT READY — RE-INTERVIEW REQUIRED <<`
```

Rules for output:

- Omit empty sections.
- No praise padding.
- No style nits unless they change implementation meaning.
- Prefer concrete citations: file paths, section names, route names, model names.
- Prioritize impact over completeness.
- If spec conflicts with code, trust codebase as canonical and flag spec.
