---
name: sddj-4-review-plan
description: Review a drafted implementation plan — checks structure, completeness, guidelines compliance, verifies technical claims against the codebase, and cross-checks coverage against the source spec. Trigger when user says "review plan", "check plan", "plan review", or "review this plan".
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which plan file should I review? (e.g. `docs/plans/feature_name_plan.md`)"

Given the plan at: $ARGUMENTS

Execute the 5 steps below sequentially.

## Steps

### Step 1 — Load reference, plan, and spec

- Read the plan template and guidelines from `.agents/skills/sddj-3-create-plan/SKILL.md` to understand the expected structure, sections, granularity rules, and principles.
- Read the plan file at the path provided in $ARGUMENTS.
- Check the plan's frontmatter for `related_docs`. If a spec path is listed, read that spec too — it will be used in Step 4 for coverage verification.
- If no spec is linked, check if a matching spec exists at `docs/plans/[feature-name]_spec.md` (derive feature name from the plan filename by replacing `_plan.md` with `_spec.md`). If found, read it and note that it was inferred rather than explicitly linked.

### Step 2 — Structural & content review

Check the plan against the template and guidelines from `sddj-3-create-plan/SKILL.md`. For each issue found, classify it as one of:

- **Missing** — a mandatory section from the template is absent
- **Incomplete** — a section exists but has TODOs, placeholders, or insufficient detail
- **Inconsistency** — two sections contradict each other (e.g. a file in the File Index not mentioned in any step, or a step referencing a file not in the File Index)
- **Clarity** — vague or ambiguous instructions that could lead to different implementations
- **Guideline violation** — violates a rule from the plan guidelines (phase ordering, step granularity, testing coverage, etc.)

Also check:

- **Phase ordering** — Backend → Shared → Frontend? Foundational changes before services before controllers?
- **Step granularity** — 1–3 files per step? No step covering 5+ files? No 3+ consecutive trivial steps that should be merged?
- **Testing coverage** — Every phase has test steps or validation blocks? No phase is implementation-only?
- **Validation criteria** — Each step has a clear way to verify completion?
- **Dependencies** — Are step dependencies stated and logically ordered?
- **Implementation Checklist** — Matches actual steps? No missing or extra entries?
- **File Index** — Matches files actually referenced in steps? No missing or extra rows?
- **Decisions Made** — Are non-obvious choices documented with rationale?
- **Unfilled placeholders** — Any `[bracketed text]` remaining (excluding markdown checkboxes `- [ ]` and HTML comments)?

### Step 3 — Codebase verification

Use Grep, Glob, and Read to verify technical claims in the plan against the actual codebase:

- **File paths** mentioned — do they exist? Are the directory structures correct?
- **Routes/endpoints** referenced — do they exist or conflict with existing ones?
- **Data models/fields** referenced — do they match the current Prisma schema?
- **Components** referenced — do they exist? Are naming conventions correct?
- **Shared types/constants** referenced — do they exist in `@kitecrew/shared`?
- **Function/method names** referenced — do they exist where claimed?
- **Dependencies** on existing features — are the integration points described accurately?
- **Patterns claimed** (e.g. "following the pattern in X") — does X actually use that pattern?

Flag any claim that doesn't match reality as a **Mismatch** issue.

### Step 4 — Spec coverage cross-check

If a spec was loaded in Step 1, verify that the plan fully covers it:

- Every requirement in the spec must map to at least one step in the plan
- Every acceptance criterion in the spec must be addressed (either by a step or in validation criteria)
- Any scope explicitly excluded in the spec should not appear as a plan step
- Any "Future Enhancements" in the spec should not be planned (unless the plan explicitly notes this as a scope expansion)

Flag any gap as a **Coverage gap** issue.

If no spec was found, note this in the report and skip this step.

### Step 5 — Present review report

Output a structured review using this format:

```
## Plan Review: [plan name]

### Summary
[1-2 sentence overall assessment — is this plan ready for implementation, or does it need work?]

### Issues Found

#### Critical (blocks implementation)
- [issue] — [explanation]

#### Structural Issues
- [issue] — [explanation]

#### Suggested Improvements
- [issue] — [explanation]

#### Codebase Mismatches
- [what the plan says] vs [what the code shows]

#### Spec Coverage Gaps
- [requirement from spec] — [not covered / partially covered]

### What's Good
[Brief callout of sections that are well-written or thorough]

### Verdict

**>> [SELECTED VERDICT] <<**

Choose exactly one and replace the line above with it:
- `>> READY FOR IMPLEMENTATION <<`
- `>> NEEDS MINOR REVISIONS (can fix inline) <<`
- `>> NEEDS SIGNIFICANT REWORK (another drafting round recommended) <<`
```

If no issues are found in a category, omit that category entirely. Do not pad the review with nitpicks — only flag things that would actually cause problems during implementation.
