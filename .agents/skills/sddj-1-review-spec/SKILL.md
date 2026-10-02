---
name: sddj-1-review-spec
description: Review a drafted spec — checks structure, completeness, guidelines compliance, and verifies technical claims against the actual codebase
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which spec file should I review? (e.g. `docs/plans/feature-name_spec.md`)"

Given the spec at: $ARGUMENTS

Execute the 5 steps below sequentially.

## Steps

### Step 1 — Pre-flight: open questions gate

Before any review work:

- Read the spec file at the path provided in $ARGUMENTS.
- Locate the `## Open Questions` section. If the section is absent, empty, or states there are no open questions, proceed to Step 2.
- For each listed open question, an item counts as **decided** only if:
  - an explicit answer or decision is recorded next to it (e.g. `A:`, `Decision:`, `Resolved:`), or
  - it is explicitly marked as solved and the answer is stated in the rest of the spec (e.g. answered in `## Key Decisions`).
- If any open question is **undecided**, HALT immediately:
  - Do NOT run Steps 2-5. Do NOT output a review report.
  - List every undecided open question.
  - Tell the user to first address/resolve these open questions, then re-run the review.

### Step 2 — Load reference and spec

- Read the spec template and guidelines from `.agents/skills/sddj-0-create-spec/SKILL.md` to understand the expected structure, sections, and principles.
- Read the spec file at the path provided in $ARGUMENTS.

### Step 3 — Structural & content review

Check the spec against the template and guidelines. For each issue found, classify it as one of:

- **Missing** — a section from the template is absent without justification
- **Incomplete** — a section exists but lacks sufficient detail or has TODOs/placeholders
- **Inconsistency** — two sections contradict each other (e.g. a field mentioned in Data Model but absent from API Changes)
- **Clarity** — vague or ambiguous language that could lead to different interpretations during implementation
- **Guideline violation** — violates a principle from the guidelines (YAGNI, KISS, DRY, component breakdown, etc.)

Also check:

- Are acceptance criteria specific and testable?
- Are edge cases and error states addressed?
- Are key decisions documented with rationale?
- Is scope clearly bounded (Out of Scope / Future Enhancements)?
- Are open questions flagged where uncertainty exists? (Undecided items here are fine for drafting, but they must have been resolved before Step 1 allowed this review to proceed.)

### Step 4 — Codebase verification

Use Grep, Glob, and Read to verify technical claims in the spec against the actual codebase:

- **Routes/endpoints** mentioned — do they exist or conflict with existing ones?
- **Data models/fields** referenced — do they match the current Prisma schema?
- **Components** referenced — do they exist? Are naming conventions correct?
- **Shared types/constants** referenced — do they exist in `@kitecrew/shared`?
- **Dependencies** on existing features — are the integration points described accurately?

Flag any claim that doesn't match reality as a **Mismatch** issue.

### Step 5 — Present review report

Output a structured review using this format:

```
## Spec Review: [spec name]

### Summary
[1-2 sentence overall assessment — is this spec ready for implementation, or does it need work?]

### Issues Found

#### Critical (blocks implementation)
- [issue] — [explanation]

#### Suggested Improvements
- [issue] — [explanation]

#### Codebase Mismatches
- [what the spec says] vs [what the code shows]

### What's Good
[Brief callout of sections that are well-written or thorough — not every review needs to be negative]

### Verdict

**>> [SELECTED VERDICT] <<**

Choose exactly one and replace the line above with it:
- `>> READY FOR IMPLEMENTATION <<`
- `>> NEEDS MINOR REVISIONS (can fix inline) <<`
- `>> NEEDS SIGNIFICANT REWORK (another drafting round recommended) <<`
```

If no issues are found in a category, omit that category. Do not pad the review with nitpicks — only flag things that would actually cause problems during implementation.
