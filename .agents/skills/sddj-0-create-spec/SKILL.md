---
name: sddj-0-create-spec
description: Create spec for new feature — writes a structured spec doc covering requirements, technical design, UI/UX, edge cases, and risks
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "What feature would you like to spec out?" before proceeding.

Given the following feature: $ARGUMENTS

Create spec doc `./docs/plans/[feature-name]_spec.md`.

## Source of Truth: Two Distinct Authorities

This skill works with two different sources of truth that answer two different questions:

**"What should we build?" -> the user-approved spec draft is authoritative.**
The spec being written is the canonical statement of intended scope, requirements, and decisions for this feature.

**"What already exists?" -> the codebase is authoritative.**
The repo reflects the real current state of the system. Existing docs may be stale.

**Consequences:**

- Use the codebase to understand current behavior, naming, structure, and constraints.
- Use user answers plus validated codebase context to define the intended future state in the spec.
- `docs/plans/*` other than the spec being drafted -> secondary context only. Verify any concrete claim against the code before reusing it.
- `docs/plans_done/*` -> archived historical context. Useful for rationale and decision history, but any specific implementation claim must be verified against the current code before being trusted.
- If another doc conflicts with the code about current reality -> silently trust the code.
- If the user's stated intent conflicts with the current code -> capture the intended change clearly in the spec rather than forcing the spec to mirror current behavior.

## Canonical Section List

Use this as the authoritative reference for which sections to write and how to handle them.

**Always include:**

| Section                                | Notes                                                        |
| -------------------------------------- | ------------------------------------------------------------ |
| YAML frontmatter                       | Added later by `/sddj-frontmatter`                            |
| `> Xmin read, for Y words and Z lines` | Added later by `/sddj-read-time`                              |
| `# [Feature Name] Spec`                | H1 title                                                     |
| `## Table of Contents`                 | Added later by `/sddj-table-of-contents`                      |
| `## Overview & Goals`                  | What this feature is, why it exists, what success looks like |
| `## Functional Requirements`           | User-visible requirements and acceptance criteria            |
| `## Technical Design`                  | Implementation shape broken into relevant subsections        |
| `## Testing Requirements`              | Behavior to protect, preferred test levels, regression focus |
| `## Out of Scope`                      | Explicit non-goals for this iteration                        |
| `## Open Questions`                    | Unresolved items needing answers or assumptions              |

**Always write the heading; fill in when applicable:**

| Section                       | When to include                                                                                      |
| ----------------------------- | ---------------------------------------------------------------------------------------------------- |
| `## Core Principles`          | Important invariants, constraints, or non-negotiables                                                |
| `## Business Rules`           | Validation, authorization, state rules, or domain constraints matter                                 |
| `### Data Model`              | New or changed entities/fields/types are involved                                                    |
| `### API / Backend Changes`   | Backend routes, controllers, services, repositories, or contracts change                             |
| `### Frontend Components`     | Frontend pages, components, state, or flows change                                                   |
| `### Migration Notes`         | Schema changes, backfills, rollout, or compatibility concerns exist                                  |
| `### Dependencies`            | The feature relies on specific internal or external dependencies                                     |
| `## UI/UX Considerations`     | The feature has meaningful flow, state, or interaction details                                       |
| `## Edge Cases & Risks`       | There are important failure modes, tricky cases, or rollout risks                                    |
| `## Key Decisions`            | Non-obvious choices or tradeoffs should be recorded                                                  |
| `## Future Enhancements`      | Scope is intentionally deferred for later                                                            |
| `## Implementation Checklist` | Always keep the heading and state that implementation planning is tracked in the separate `_plan.md` |
| `## References`               | There are useful related docs, specs, issues, or PRs to link                                         |

When writing the spec:

- Write every section heading from both lists into the document.
- If a section is relevant, fill it with concrete content.
- If a section is intentionally not used for this feature, keep the heading and add a short explicit note such as `Intentionally skipped.`
- Do not silently omit section headings. The document should make skipped areas explicit to future readers.

---

Guidelines:

- follow best practices and established design patterns
- follow known principles such as: YAGNI (You Aren't Gonna Need It), KISS (Keep It Simple), DRY (Don't Repeat Yourself)
- keep the spec implementation-agnostic; a separate `_plan.md` is always required, and TDD is a delivery mode chosen later in that plan, not a separate kind of spec
- if UX involved: follow UX patterns and current design trends
- if frontend involved: break out the page/component in sub-components to improve maintainability, code quality and readability.
- if in doubt, explain several potential solutions along with tradeoffs.

Execute the 5 steps below sequentially.

0. Gather context:

- Infer a snake_case feature name from $ARGUMENTS (e.g. `user_profile_edit`, `trip_booking_flow`).
- Use a single `AskUserQuestion` call with two questions:
  1. Confirm the feature name (snake_case) — offer 2-3 inferred options or let the user provide their own.
  2. Which files or folders are most relevant? (e.g. existing pages, components, API routes, docs, DB schema)
- Do a quick codebase scan (Glob/Grep) to check if anything else looks relevant that the user may not have mentioned.
- Read and analyse all of the above before proceeding.
- Assume a separate implementation plan doc at `./docs/plans/[feature-name]_plan.md` will exist for this feature. The spec must not act as the only implementation doc.

1. Interview the user across the spec sections.

**1a. Calibrate depth first:**

- Based on the feature complexity, estimate the recommended spec depth, expected question count, and time.
- Use `AskUserQuestion` with two questions:
  1. Preferred spec depth — offer these 5 options, marking your recommendation and adjusting labels to reflect your estimate (e.g. `Standard (~12 questions) (Recommended)`):
     - Minimal (~1-3 questions, 1 short round, 2-5 min) — trivial change, narrow scope, only core sections
     - Compact (~4-7 questions, 1-2 rounds, 5-10 min) — small feature, select optional sections only
     - Standard (~8-12 questions, 2-3 rounds, 10-20 min) — typical feature, all always-required sections plus relevant optional ones
     - Detailed (~12-20 questions, 3-4 rounds, 20-35 min) — complex feature touching multiple concerns, most optional sections included
     - Exhaustive (~20-35 questions, 4-5 rounds, 35-55 min) — large-scope or high-risk feature, all relevant sections explored in depth
  2. Optional sections — which optional sections should receive substantive content? Make it explicit that selected options will be filled in, while unselected ones will still appear in the spec as headings with an explicit `not applicable / intentionally skipped` note. Available: Core Principles, Business Rules, Data Model, API / Backend Changes, Frontend Components, Migration Notes, Dependencies, UI/UX Considerations, Edge Cases & Risks, Key Decisions, Future Enhancements, References.
     - Do not offer `Implementation Checklist` as a selectable option. It must always remain a short pointer to the separate `_plan.md`, not a task list inside the spec.
- Adjust the rounds below to match the chosen depth and the sections selected for substantive content.

**1b. Run up to 3 focused rounds (max 4 questions each). Skip rounds not applicable:**

- **Round A — Product & Scope** (Overview & Goals, Core Principles, Functional Requirements, Future Enhancements, Out of Scope):
  Goals, target users, success criteria, key user stories, design constraints/invariants, what's explicitly out of scope, what's deferred to a future iteration.

- **Round B — Technical** (Data Model, API / Backend Changes, Frontend Components, Business Rules, Migration Notes, Dependencies):
  New/modified entities, API surface, component breakdown, state management, business/validation rules, migration and rollout concerns, integration points with existing code, dependencies on existing features/shared components/services/external libs, other in-progress work that might conflict.

- **Round C — UX, Risks & Quality** (UI/UX Considerations, Edge Cases & Risks, Testing Requirements, Key Decisions):
  Key flows, empty/loading/error states, edge cases, hard parts, potential regressions, testing approach (unit/integration/E2E), which behaviors most need regression protection, whether the user expects TDD during implementation, key architectural or product decisions and their rationale.

Dig into parts the user might not have considered. Raise tradeoffs and concerns where relevant.

**1c. Check-in before proceeding:**

- Use `AskUserQuestion` to ask: "Happy to proceed to writing the spec, or would you like to refine further with more questions?"
- If the user wants more questions, treat this as a continuation of the interview:
  - Re-run **1a** (calibrate): based on what's already been covered, estimate the remaining questions and time (e.g. "I can run ~6 more questions (~6 min) across 1-2 rounds"). Offer depth options and ask if any new areas should be added or existing ones revisited.
  - Re-run **1b** (focused rounds), adjusted: run only the rounds needed. You may add a **Round D — Gaps** for uncovered areas or deeper dives not yet explored.
  - Then loop back to **1c**.

2. Write the spec draft:

Before writing, use extended thinking to reason through: how all gathered requirements connect, potential conflicts or gaps, the right structure and ordering of sections, and any tradeoffs worth capturing in Key Decisions.

- Write the spec to the file, including every canonical section heading from the section list.
- Fill relevant sections with substantive content.
- For sections that do not apply, keep the heading and add an explicit short note that they were considered and intentionally left without substantive content.
- Keep `## Implementation Checklist` and state that implementation planning is tracked in the separate `_plan.md`.
- In `## Testing Requirements`, capture testing intent in behavior terms: what must be covered, the preferred test levels, critical bug/regression risks, and whether the user prefers TDD for implementation. Do not turn the spec into a RED/GREEN/REFACTOR task list.

3. Triple check inconsistencies:

- Use Grep/Read to verify the spec is fully aligned with the current implementation.
- The code is canonical for current-state claims only. If the spec misstates what already exists, update the spec to match the code. If the spec is describing an intended future-state change, keep that intent and make sure the delta from current behavior is explicit.
- If there are inconsistencies that require a **codebase change** (not just a spec correction), flag those to the user and ask to confirm before making any changes.

4. Finally, add/update the doc metadata, run sequentially:

- /sddj-frontmatter — set status_doc to "draft" automatically (do not ask the user, this is a new spec)
- /sddj-imp-status — set status to "PENDING: spec must be reviewed before implementation can start" automatically (do not ask the user, this is a new draft spec)
- /sddj-table-of-contents
- /sddj-read-time
