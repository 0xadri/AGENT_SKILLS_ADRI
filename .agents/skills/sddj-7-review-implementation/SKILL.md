---
name: sddj-7-review-implementation
description: Review code changes (committed or uncommitted) against an implementation plan. Checks plan compliance, CFL verification, checklist status, and optionally code quality. Offers to archive the plan and spec when the review passes. Trigger when user says "review implementation", "review changes against plan", "check implementation", or "review impl".
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which plan file should I review the implementation against? (e.g. `docs/plans/feature_name_plan.md`)"

## Guidelines

- Never modify implementation code files — this skill is read-only for code.
- The only files this skill may write to are plan and spec files, and only during the archive step (Step 8).
- Use the plan structure conventions from `.agents/skills/sddj-6-implement-plan/SKILL.md` to understand expected plan format and terminology.
- Always determine the change detection method (committed vs uncommitted) before analyzing.
- When reviewing code quality, apply the project conventions from `.claude/CLAUDE.md` — naming, layer order, error handling, etc.
- Be specific: cite file paths and line numbers when flagging issues.
- Don't pad the review with nitpicks — only flag things that matter.

## Steps

Execute the steps below sequentially.

### Step 1 — Resolve the plan path

**Case A — `$ARGUMENTS` looks like a file path** (contains `/` or ends with `.md`):

- Use it directly as `[plan-path]`.

**Case B — `$ARGUMENTS` is a plain name** (no `/`, doesn't end with `.md`):

- Normalize: replace hyphens with underscores.
- Glob for `docs/plans/[name]_plan*.md`.
  - One match → use it.
  - Multiple → list them and ask which one.
  - None → inform the user and stop.

Read `[plan-path]`. If it doesn't exist, stop and inform the user.

### Step 2 — Detect changes

Determine whether the implementation changes are committed or uncommitted:

1. Run `git diff HEAD --stat` to check for uncommitted changes.
2. If there are uncommitted changes (staged or unstaged), use `git diff HEAD` as the change set.
3. If no uncommitted changes, use the most recent commit(s). Run `git log --oneline -10` and show the recent commits. Ask the user: "No uncommitted changes found. How many recent commits should I review against this plan?" Default to the last commit if the user doesn't specify.
4. Use `git diff <base>..HEAD` for the selected commit range, and `git show --stat <commits>` for context.

Store the full diff output for analysis in subsequent steps.

### Step 3 — Plan compliance review

Read the plan's `## Implementation Checklist Summary` and each phase/step section.

For each step in the plan:

1. **Covered?** — Do the changes include modifications to the files and logic described in this step?
2. **Faithful?** — Does the implementation match what the plan specified (correct files, correct approach, correct data flow)?
3. **Extra work?** — Are there changes in the diff that don't correspond to any plan step? (Flag as unplanned changes — not necessarily bad, but worth noting.)
4. **Missing?** — Are there plan steps with no corresponding changes in the diff?

### Step 4 — Checklist, CFL & status sync check

#### 5.1 — CFL Verification (hard block)

Look for a `## CFL Verification` section in the plan.

**If the section is missing:**
Stop immediately and output:

> **HARD BLOCK — CFL Verification section not found.** `sddj-6-implement-plan` Step 5 has not run for this plan. The implementation review cannot proceed until all CFL checks have been completed and checked off. Re-run `/sddj-6-implement-plan` to execute Step 5.

**If the section exists but any item is still unchecked (`[ ]`):**
Stop immediately and list the unchecked items, then output:

> **HARD BLOCK — The following CFL checks have not been completed: [list unchecked items].** `sddj-6-implement-plan` Step 5 did not complete fully. The implementation review cannot proceed until all CFL items are checked off.

Only continue to 4.2 if all CFL items are checked.

#### 5.2 — Checklist & status consistency

Check the plan document's current state:

- Are the `## Implementation Checklist Summary` checkboxes consistent with the actual changes? (e.g., items marked `[x]` that have no corresponding code changes, or unchecked items that appear to be implemented)
- Is the `## IMPLEMENTATION STATUS` header consistent with the checklist state?
- If a spec file exists (check `related_docs` frontmatter or `docs/plans/[feature-name]_spec.md`), is its status in sync with the plan?

Flag any inconsistencies.

### Step 5 — Code quality review

Review the changed code against project conventions (from `.claude/CLAUDE.md`):

- **Layer discipline** — Routes → Controllers → Services → Repositories. No skipped layers, no business logic in controllers.
- **Naming** — PascalCase components, camelCase functions, snake_case DB columns.
- **Error handling** — Uses project error classes (`NotFoundError`, `ValidationError`, etc.), not generic `Error`. Errors thrown at the right layer.
- **TypeScript** — No `any` types. Uses shared types from `@kitecrew/shared` where applicable.
- **Patterns** — Follows existing patterns in the codebase for similar features. Uses Winston logger (not `console.log`), cuid2 for IDs, Zod for validation.
- **Security** — No obvious injection vectors, proper input validation at API boundaries.
- **Edge cases** — Obvious missing null checks, unhandled error paths, or race conditions.

Use Grep and Read to compare against existing similar code in the codebase when checking pattern adherence.

### Step 6 — Present review report

Output a structured review:

```
## Implementation Review: [plan name]

### Summary
[1-2 sentence overall assessment — does the implementation match the plan?]

### Plan Compliance

#### Fully Implemented Steps
- [step] — [brief note]

#### Partially Implemented Steps
- [step] — [what's missing or different]

#### Missing Steps
- [step] — [not found in changes]

#### Unplanned Changes
- [file/change] — [not in plan, note if it seems intentional]

### CFL Verification
[All items checked — or list any unchecked items]

### Checklist & Status
[Status of checklist accuracy and sync state — or "All consistent" if no issues]

### Code Quality Issues

#### Critical
- [file:line] — [issue]

#### Suggestions
- [file:line] — [suggestion]

### What's Good
[Brief callout of well-implemented parts]

### Verdict
[ ] Implementation matches the plan — ready to close
[ ] Minor gaps — small fixes needed
[ ] Significant deviations — discussion needed
```

Omit empty categories. Don't pad with nitpicks.

### Step 7 — Record the review in the plan

Append an entry to the `## Review History` section of `[plan-path]`. If the section does not exist (e.g. older plan without the template), add it immediately before `## File Index`, or at the end of the file if `## File Index` is also absent.

Use the Edit tool to insert the following block immediately after the `## Review History` header line (and its HTML comment, if present), before any previous entries — most recent review first:

```markdown
### Review — [YYYY-MM-DD] · [model-name]

- **Verdict:** [one of: "Clean — ready to archive" / "Suggestions only" / "Gaps found — missing plan steps" / "Critical issues — fixes required"]
- **Issues:** [N critical, N suggestions — or "None"]
- **Fixes applied:** [list of issues resolved since the previous review, or "N/A — first review" / "None — issues remain open"]
```

Derive each field from the review output:

- **Date:** use today's date from the system context
- **Model:** use the model currently running this skill (check `model` frontmatter of this skill file as a fallback — `claude-opus-4-6`)
- **Verdict:** map directly from the Verdict checkbox selected in Step 6
- **Issues:** count from the Critical and Suggestions sections of the Step 6 report
- **Fixes applied:** compare against the previous review entry (if one exists) — list issues from that entry that are no longer present in the current review; if this is the first review, write "N/A — first review"

### Step 8 — Offer to archive

Only run this step if **all** of the following are true:

- The plan is located in `docs/plans/` (not already archived in `docs/plans_done/`)
- All items in `## Implementation Checklist Summary` are checked
- All items in `## CFL Verification` are checked

If any condition is not met, skip this step entirely.

**Determine the recommendation** based on the review outcome:

- No critical issues found in Step 5 → **recommend Yes** ("implementation looks clean")
- Critical issues found in Step 5 → **recommend No** ("fix critical issues before archiving")

**Resolve the spec path** from the plan's `related_docs` frontmatter (look for a path ending in `_spec.md`). If found, verify it exists. If not found or doesn't exist, fall back to `./docs/plans/[feature-name]_spec.md`. If neither exists, proceed with plan-only archive and note the spec was not found.

**Check for destination conflicts** before moving anything:

- `./docs/plans_done/[feature-name]_plan.md`
- `./docs/plans_done/[feature-name]_spec.md`

If either already exists, warn the user: "A file with this name already exists in `docs/plans_done/`. Overwriting it could destroy a previously archived version. Rename the existing file first, then retry." Then stop.

**Ask the user:**

> "Archive the plan and spec to `docs/plans_done/`?
>
> - **[Yes — Recommended / Not recommended]**: [one-line rationale from the review outcome]
> - **No**: leave files in place"

If the user confirms:

1. Ensure `./docs/plans_done/` exists (`mkdir -p` via Bash).
2. Update `status_doc` in the frontmatter of both `[plan-path]` and `[spec-path]` to `"completed"` using the Edit tool.
3. Move `[plan-path]` → `./docs/plans_done/[feature-name]_plan.md` via Bash.
4. Verify the plan move succeeded: confirm it exists in `docs/plans_done/` and no longer exists in `docs/plans/`. If this fails, stop and warn the user: "The plan move failed — no files have been changed. Verify permissions and retry." Do not proceed to move the spec.
5. Move `[spec-path]` → `./docs/plans_done/[feature-name]_spec.md` via Bash.
6. Verify the spec move succeeded: confirm it exists in `docs/plans_done/` and no longer exists in `docs/plans/`. If this fails, **roll back**: move the plan back from `docs/plans_done/[feature-name]_plan.md` → `[plan-path]`, then warn the user: "The spec move failed and the plan has been moved back to `docs/plans/` — no archive was completed. Verify the spec file and retry."
7. If both verifications pass, inform the user: "Both the plan and spec for **[feature-name]** have been archived to `docs/plans_done/` — implementation is complete."

If the user declines: confirm and stop.
