---
name: sddj-5-review-plan-adversarial
description: Review implementation plan adversarially — attack execution risk, sequencing gaps, hidden scope, policy conflicts, and false confidence before coding starts. Use when user asks for adversarial plan review, hostile plan review, plan gate, or pre-implementation risk review.
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which plan file should I review adversarially? (e.g. `docs/plans/feature_name_plan.md`)"

Given plan at: $ARGUMENTS

Execute 6 steps below sequentially.

## Steps

### Step 1 — Load references, repo rules, plan, spec

- Read `.agents/skills/sddj-3-create-plan/SKILL.md` to understand expected plan structure, phase ordering, granularity rules, TDD rules, and validation expectations.
- Read repo instructions from `AGENTS.md` and any repo-local constraints relevant to shared types, `API_ROUTES`, Zod schemas, Prisma changes, dependencies, auth/security, architecture boundaries, and testing/build order.
- Read plan file at path provided in $ARGUMENTS.
- Check plan frontmatter for `related_docs`. If spec path is listed, read that spec too.
- If no spec is linked, check whether matching spec exists at `docs/plans/[feature-name]_spec.md` by deriving feature name from plan filename. If found, read it and note that it was inferred rather than explicitly linked.

### Step 2 — Structural and literal-execution attack

Review plan as if engineer will implement it literally, step by step, with no helpful interpretation.

Check for:

- Missing mandatory sections from plan template without justification.
- Incomplete sections, TODOs, placeholders, vague validation, or hand-wavy file references.
- Internal contradictions across phases, steps, checklist, file index, decisions, or frontmatter.
- Ambiguity: places where two competent engineers could implement different code from same step.
- False precision: exact-looking steps that still hide critical choices, assumptions, or acceptance boundaries.
- Plan/spec leakage: requirements decisions changed in plan without being called out as scope or assumption.
- False confidence: checklist, validation, or file index makes plan look complete while meaningful implementation risk remains uncovered.

Classify each issue as one of:

- **Critical** — likely wrong implementation, blocked execution, broken contract, policy violation, or unsafe rollout.
- **Major** — likely rework, drift, sequencing pain, or under-specified implementation.
- **Minor** — useful clarification, but not likely to derail execution alone.

### Step 3 — Sequencing, dependency, and execution-risk attack

Inspect whether plan can be executed safely in listed order.

Check for:

- Phase ordering violations relative to repo rules: backend before shared before frontend when applicable.
- Missing prerequisites: step depends on file, API, type, data, or test setup not created yet.
- Invalid intermediate states: plan leaves app or contract broken between phases/steps without acknowledging it.
- Hidden coupling: one step quietly requires changes in files owned by later steps.
- Over-coarse steps that bundle too many risky changes together to validate safely.
- Over-fine steps that create churn or meaningless checkpoints without independent verification value.
- TDD plans that are not actually executable in `RED -> GREEN -> REFACTOR` slices.
- Validation commands too weak to catch likely regressions for step risk.
- Missing final gate needs: type/lint, tests, seed, build, or package-order verification where scope requires them.

### Step 4 — Hard-stop, architecture, and rollout scan

Explicitly inspect whether plan proposes or implies any repo hard-stop area.

Check for:

- shared type changes in `packages/shared/`
- `API_ROUTES` changes
- Zod schema contract changes
- Prisma schema changes or migrations
- auth/security changes
- new dependencies
- backend layer boundary violations (routes -> controllers -> services -> repositories -> database)

For each hard-stop area found:

- say whether plan acknowledges impact or treats it like ordinary edit
- say whether explicit user confirmation would be required before implementation
- flag omission as issue if plan hides, understates, or sequences it unsafely

Also check rollout and change-management risk where relevant:

- schema/data migration ordering
- API contract transition for existing callers
- backfill or historical data handling
- partial rollout safety across backend/frontend mismatch windows
- logging/observability if failure could be silent or hard to debug

Do not require every topic for every plan. Only flag missing items when feature risk or scope makes them necessary.

### Step 5 — Spec and codebase verification

Use `Grep`, `Glob`, and `Read` to verify plan against both spec and actual codebase.

Verify at minimum:

- file paths referenced in steps and file index
- routes/endpoints mentioned
- Prisma models/fields/enums mentioned
- components/pages/hooks/services referenced
- shared types/constants referenced
- patterns the plan claims it will follow
- dependencies or tools the plan assumes already exist

If spec was loaded, also verify:

- every spec requirement maps to at least one plan step or explicit validation item
- out-of-scope items from spec are not silently planned
- future enhancements are not pulled into implementation unless called out as scope expansion
- plan does not alter settled product behavior from spec without documenting decision/risk

Flag each issue as:

- **Mismatch** — plan contradicts codebase or spec
- **Stale reference** — path, filename, symbol, or example pattern is wrong or outdated
- **Coverage gap** — spec requirement has no executable plan coverage
- **Unsupported claim** — plan states something as true but codebase or spec does not support it

### Step 6 — Present adversarial review report

Output review in this format:

```
## Adversarial Plan Review: [plan name]

### Summary
[1-3 sentences. State whether plan is safe to execute, risky, or not ready.]

### Critical Findings
- [issue] — [why this can cause wrong implementation / blocked execution / policy break]

### Major Findings
- [issue] — [impact]

### Minor Findings
- [issue] — [impact]

### Hard-Stop / Confirmation Required
- [area] — [why explicit user confirmation is required before implementation]

### Sequencing / Execution Risks
- [step or phase risk] — [what likely goes wrong if executed as written]

### Codebase or Spec Mismatches
- [what plan says] vs [what codebase/spec shows]

### Coverage Gaps
- [spec requirement or implementation concern] — [not covered / weakly covered]

### Literal-Execution Risks
- [if engineer follows this plan exactly, what likely goes wrong]

### Verdict

**>> [SELECTED VERDICT] <<**

Choose exactly one and replace line above with it:
- `>> SAFE TO EXECUTE <<`
- `>> REVISE BEFORE IMPLEMENTATION <<`
- `>> NOT SAFE — RE-PLAN REQUIRED <<`
```

Rules for output:

- Omit empty sections.
- No praise padding.
- No style nits unless they change execution meaning.
- Prefer concrete citations: file paths, section names, step numbers, route names, model names.
- Prioritize impact over completeness.
- If plan conflicts with codebase, trust codebase as canonical for current state and flag plan.
- If plan conflicts with spec on intended behavior or scope, flag it explicitly rather than silently choosing one.
