---
name: sddj-6-implement-plan
description: Implement a plan document step by step, then sync implementation status across the plan and spec, and archive both when complete. Trigger when user says "implement plan", "execute plan", "follow the plan", or "mark plan done".
---

## Guidelines

- Always resolve the plan path before doing anything else.
- Treat TDD as a plan mode, not as a separate workflow. If the plan frontmatter sets `implementation_mode: "tdd"`, execute each checklist step as **RED → GREEN → REFACTOR**.
- If `implementation_mode` is `direct` (or absent), regularly run typechecking and targeted single test files while implementing, not only at the end of the full checklist.
- Never skip the status sync — it must run whether the implementation just started, is mid-progress, or is complete.
- Archive only when every checklist item is checked, all success criteria (if present) are met, CFL has passed cleanly, and only after explicit user confirmation.
- Do not modify checklist item text — only their checked state (`[ ]` → `[x]`) belongs to the implementer, not this skill.
- Phase status emojis must always reflect reality: derive them from the checklist, never guess.
- Always resolve the spec path from the plan's `related_docs` frontmatter before falling back to the naming convention — but verify the resolved path exists before trusting it.
- Never silently correct inconsistencies — surface them to the user first.
- Never assume a destructive operation succeeded — always verify the outcome.
- **Never stop after Step 4 without syncing status.** Step 4 must always be followed by Step 3. Run Step 5 only when the checklist has reached completed state; otherwise stop after the sync and report that final CFL is deferred until plan completion.
- If pausing for context reasons, do it only once per phase boundary inside Step 4 before the first step of the next phase. Do not pause before each step, do not pause mid-phase, and do not add context-risk pauses elsewhere.

## Steps

Execute the steps below sequentially.

### Step 1 — Resolve the plan path

**Case A — `$ARGUMENTS` looks like a file path** (contains `/` or ends with `.md`):

- Use it directly as `[plan-path]`.
- Extract the feature name: strip the directory prefix and the `_plan.md` suffix (e.g. `docs/plans/user_trips_plan.md` → `user_trips`).

**Case B — `$ARGUMENTS` is a plain name** (no `/`, doesn't end with `.md`):

- Normalize it: replace hyphens with underscores.
- Use Glob with pattern `docs/plans/[feature-name]_plan*.md` to find all matching plan files.
  - If exactly one match → use it as `[plan-path]`.
  - If multiple matches (e.g. `_plan.md`, `_plan_v2.md`, `_plan_v3.md`) → list them and ask the user: "Multiple plan versions exist for this feature. Which one would you like to use?" Wait for the user to select one before proceeding.
  - If no matches → set `[plan-path]` to `./docs/plans/[feature-name]_plan.md` and let the not-found check below handle it.

**Case C — `$ARGUMENTS` is empty**:

- List available plan files using Glob with pattern `docs/plans/*_plan.md`, strip directory prefix and `_plan.md` suffix, and show the feature names.
- Ask the user which plan to implement, then apply Case B logic.

**After resolving `[plan-path]`:**

If `[plan-path]` contains `plans_done/` anywhere in the path, stop immediately and warn the user: "This path points to an archived plan in `docs/plans_done/` — it was marked as fully implemented. You should not re-execute an archived plan. If you need to reopen it, move it back to `docs/plans/` manually first."

If the file does not exist at `[plan-path]`, check whether it exists at `./docs/plans_done/[feature-name]_plan.md`:

- If found there: warn the user — "This plan has already been archived to `docs/plans_done/` — it was marked as fully implemented. You should not re-execute an archived plan. If you need to reopen it, move it back to `docs/plans/` manually first." Then stop.
- If not found anywhere: inform the user the plan file could not be found and stop.

**Inconsistent archive state check:** If the plan exists at `[plan-path]` (in `docs/plans/`), also check whether `./docs/plans_done/[feature-name]_spec.md` exists. If it does, warn the user: "The spec for this feature is already archived in `docs/plans_done/` but the plan is still in `docs/plans/` — these files are out of sync. Resolve the inconsistency manually before proceeding." Then stop.

From this point forward, `[feature-name]` and `[plan-path]` are fully resolved.

### Step 2 — Validate the plan and present its state

Read `[plan-path]`.

**Draft warning:** Check the plan's `status_doc` frontmatter field. If its value is `"draft"` (or missing entirely), warn the user: "This plan is still marked as draft and may not be finalized. Implementing it now could result in rework if the plan changes. Proceed anyway?" Stop if the user says no.

**Checklist guard:** Check whether the plan contains a `## Implementation Checklist Summary` section. If it does not exist (or is named differently and cannot be found), stop and inform the user: "This plan has no `## Implementation Checklist Summary` section — implementation status cannot be tracked or synced. Add one before proceeding."

**Empty checklist guard:** If the `## Implementation Checklist Summary` section exists but contains zero checklist items (no `[ ]` or `[x]` lines), stop and inform the user: "The checklist section exists but has no items — this is likely a malformed plan. Add checklist items before proceeding." Do not treat a zero-item checklist as COMPLETED.

**Present a compact summary:**

- Current `implementation_mode` (`direct` if absent, otherwise use the frontmatter value)
- Current `## IMPLEMENTATION STATUS` header value
- Phase breakdown with their current emoji/label (⏳ Pending / 🔄 In Progress / ✅ Done)
- Total checklist items: X checked, Y remaining
- **Next unchecked step:** find the first unchecked item (`[ ]`) in `## Implementation Checklist Summary` and show it (e.g. "Next: Step 1.2 · Add route handler")

**Then branch based on checklist state:**

**If some or all items are already checked** (mid-progress or potentially complete):

- Go directly to Step 3 to sync status first. Step 3.6 will handle routing after the sync.

**If no items are checked** (nothing started yet):

- Ask the user: "Ready to start implementing? Or would you like to sync the status first without implementing?"
  - If they want to implement first → proceed to Step 4 (implementation).
  - If they want to sync now → proceed to Step 3.

### Step 3 — Sync implementation status

**3.1 — Resolve the spec path**

Read `[plan-path]` and check its `related_docs` frontmatter for paths ending in `_spec.md`.

If one spec path is found in `related_docs`:

- Verify the file actually exists at that path.
- If it exists → use it as `[spec-path]`.
- If it does not exist → warn the user: "The spec path in `related_docs` (`[path]`) does not exist — it may be stale or the file was moved. Falling back to naming convention." Then fall back to `./docs/plans/[feature-name]_spec.md`.

If multiple spec paths are found in `related_docs`:

- List them and ask the user: "Multiple spec files are listed in `related_docs`. Which is the primary spec to sync status against?" Wait for selection before proceeding.

If `related_docs` is absent or contains no spec path, fall back to `./docs/plans/[feature-name]_spec.md`.

If neither the frontmatter path nor the fallback path exists, skip step 3.5 and warn the user: "Could not find a spec file for this plan — status sync to spec was skipped."

**3.2 — Determine the current overall status**

Count checked (`[x]`) vs unchecked (`[ ]`) items in `## Implementation Checklist Summary`:

- All checked, none unchecked → **COMPLETED**
- Some checked, some unchecked → **IN PROGRESS**
- None checked → **READY FOR IMPLEMENTATION**

Also check `## Success Criteria` (if present) — if any criteria are unmet, status cannot be COMPLETED regardless of checklist.

**3.3 — Surface status inconsistencies**

Compare the derived status (from the checklist) against the current `## IMPLEMENTATION STATUS` header value. If they differ, surface the discrepancy before making any edits:

- Example: header says `IMPLEMENTATION COMPLETED` but 3 checklist items are unchecked → warn: "The plan header says IMPLEMENTATION COMPLETED but 3 checklist items are still unchecked. This may indicate skipped steps or a manual edit. The header will be corrected to IN PROGRESS to match the checklist. Proceed?"
- Example: header says `READY FOR IMPLEMENTATION` but all items are checked → note: "The checklist is fully checked but the header hasn't been updated. It will be corrected to IMPLEMENTATION COMPLETED."

Stop and wait for user confirmation before proceeding if the discrepancy could indicate skipped work (i.e. header claims more progress than the checklist shows). For the inverse case (checklist ahead of the header), proceed automatically — it is just a stale header.

**3.4 — Update status sections in the plan**

Edit `[plan-path]` to reflect the derived status:

- **`## IMPLEMENTATION STATUS`** header — update the label:
  - → `IMPLEMENTATION STATUS: READY FOR IMPLEMENTATION`
  - → `IMPLEMENTATION STATUS: IN PROGRESS`
  - → `IMPLEMENTATION STATUS: IMPLEMENTATION COMPLETED`
- **Phase lines** under the status header — update each phase's emoji and label based on its own checklist items:
  - All items checked → `✅ Done`
  - Some items checked → `🔄 In Progress`
  - No items checked → `⏳ Pending`
- Do not rewrite checklist item text — only update the section header and phase emoji/labels.

**3.5 — Sync status to the spec**

Read `[spec-path]`.

Find its `## IMPLEMENTATION STATUS` section (it may be labelled differently, e.g. `## Implementation Status`). Update it to match the plan's new overall status label exactly.

If the spec has no `## IMPLEMENTATION STATUS` section, add one immediately after the frontmatter block with the current status.

**3.5.1 — Refresh derived Table of Contents entries**

If `[plan-path]` contains a `## Table of Contents` section, regenerate that TOC so any `IMPLEMENTATION STATUS: ...` entry and anchor match the updated status heading text.

If `[spec-path]` exists and contains a `## Table of Contents` section, regenerate that TOC too for the same reason.

**3.6 — Report status and route**

Branch based on the overall status derived in 3.2:

**If COMPLETED** (all items checked, all success criteria met):

- Report that the checklist is fully done.
- Proceed to Step 5.

**If IN PROGRESS** (unchecked items remain):

- Report the current status summary (e.g. "8 of 12 checklist items done — implementation is in progress").
- Proceed to Step 4 and follow the phase-boundary pause rules there.

**If READY FOR IMPLEMENTATION** (no items checked — sync-only path):

- Report the status and stop. No further steps.

### Step 4 — Execute implementation

This step runs when implementation should proceed. Work through unchecked items phase by phase. Do not stop within a phase unless a blocker is encountered.

Before processing checklist items, read the plan frontmatter and set `[implementation-mode]`:

- If `implementation_mode: "tdd"` → `[implementation-mode] = tdd`
- If `implementation_mode: "direct"` or the field is absent → `[implementation-mode] = direct`
- If the value is anything else → stop and ask the user to correct the plan frontmatter before proceeding

Determine current phase by locating the first unchecked checklist item, then identifying all remaining unchecked items that belong to that same phase. Complete that phase before considering any pause.

Before starting the current phase, ask the user once for that phase, before the first unchecked step in that phase:

- "Do you want to continue in this session?"
- "Do you want a handoff prompt? Running low on context is possible as implementation continues."
- If they choose to continue in this session → execute the full current phase without asking again between steps.
- If they choose a handoff prompt → run `/get-handoff-prompt`, return its output, then stop.

For each unchecked checklist item in the current phase:

1. Identify the corresponding section in the plan (match the step number and title, e.g. "Step 1.1").
2. Read that section's required fields based on `[implementation-mode]`:
   - `direct` → **Files**, **Changes**, and **Validation**
   - `tdd` → **Files**, **Test Level**, **RED**, **GREEN**, **REFACTOR**, and **Validation**
3. If any required field for that mode is missing, stop and report the malformed plan step to the user. Do not guess.
4. Execute the step according to `[implementation-mode]`:
   - `direct` mode:
     - Make all file changes described — create new files or edit existing ones as specified.
     - Regularly run typechecking and targeted single test files as you go, especially after meaningful edits, so regressions surface before the step's final validation.
     - Run the validation check described in the step. If it requires building or running tests, do so via Bash.
   - `tdd` mode:
     - **RED** — add the failing test first.
     - Run the relevant test command and confirm it fails for the expected reason. If the test passes immediately, stop and report it; the RED step did not prove the behavior.
     - **GREEN** — make the minimum code change needed to make the failing test pass.
     - Run the relevant test command again and confirm it now passes.
     - **REFACTOR** — perform only the cleanup explicitly allowed by the step.
     - Run the relevant test command again after refactoring to confirm behavior still passes.
5. If validation passes for the step's mode: check the item in `## Implementation Checklist Summary` by changing `[ ]` to `[x]`.
6. If validation fails at any point: stop, report the failure and the blocker to the user, and do not mark the item as done.

**CRITICAL — do not stop here without syncing status.** After completing the current phase (or after hitting a blocker), immediately continue to Step 3 — do not pause, summarize, or wait for input.

After Step 3 finishes:

- If the overall status is now **COMPLETED**, continue to Step 5.
- If the overall status is still **IN PROGRESS** after a completed phase, report the synced status and return to Step 4 for the next phase.
- If the overall status is **READY FOR IMPLEMENTATION**, stop after reporting the synced status. Final CFL is deferred until the plan is complete.

### Step 5 — Close the feedback loop

**When to run:** Run only when Step 3 determines the plan is **COMPLETED**. Do not run full CFL for a merely in-progress TDD slice; per-step validation already happened inside Step 4.

Run these skills sequentially. After each skill passes, immediately check off its corresponding item in `## CFL Verification` in `[plan-path]`:

1. `/cfl-type-and-lint` → on pass: `[ ] Type & Lint` → `[x] Type & Lint`
2. `/cfl-tests-fe-and-be` → on pass: `[ ] Tests (frontend + backend)` → `[x] Tests (frontend + backend)`
3. `/cfl-seed-db` — only if there were database-impacting changes (new tables, schema changes, migrations, or seed data) → on pass: `[ ] Seed DB` → `[x] Seed DB`; if skipped: `[ ] Seed DB` → `[x] Seed DB — N/A`
4. `/cfl-build` → on pass: `[ ] Build` → `[x] Build`

**Guardrails:**

- **Stop on failure, don't continue.** If a CFL skill surfaces errors that cannot be fixed, stop immediately and report to the user. Do not proceed to the next CFL skill.
- **Cap fix attempts.** If the same error persists after 2–3 fix attempts, stop and surface it to the user rather than looping.
- **Do not re-sync the plan status to COMPLETED.** If CFL fails, leave the status as IN PROGRESS until all checks pass.
