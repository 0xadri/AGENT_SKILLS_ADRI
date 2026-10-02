---
name: sddj-8-review-implementation-adversarial
description: Review code changes adversarially against a plan and spec — attack ship risk, hidden contract drift, weak validation, rollout hazards, and false confidence after implementation exists.
---

If $ARGUMENTS is empty, use `AskUserQuestion` to ask: "Which plan file should I review adversarially? (e.g. `docs/plans/feature_name_plan.md`)"

## Guidelines

- This skill is read-only for code, spec files, and plan content. The only file this skill may write to is the plan file, and only to append a Review History entry (Step 10).
- This is not a compliance checklist clone of `sddj-7-review-implementation`.
- Review with hostile ship mindset: assume checklist is green, tests may pass, and code may still be unsafe.
- Prioritize impact over completeness.
- Cite file paths and line numbers whenever possible.
- Do not pad with praise, style nits, or low-value suggestions.
- Trust the codebase as canonical for current behavior.
- Trust the spec and plan as canonical for intended behavior, and flag drift explicitly rather than silently choosing one.

## Steps

Execute the steps below sequentially.

### Step 1 — Resolve the plan path

**Case A — `$ARGUMENTS` looks like a file path** (contains `/` or ends with `.md`):

- Use it directly as `[plan-path]`.

**Case B — `$ARGUMENTS` is a plain name** (no `/`, doesn't end with `.md`):

- Normalize: replace hyphens with underscores.
- Glob for `docs/plans/[name]_plan*.md`.
  - One match -> use it.
  - Multiple -> list them and ask which one.
  - None -> inform the user and stop.

Read `[plan-path]`.
If it does not exist, stop and inform the user.

### Step 2 — Detect changes under review

Determine whether the implementation changes are committed or uncommitted:

1. Run `git diff HEAD --stat` to check for uncommitted changes.
2. If there are uncommitted changes, use `git diff HEAD` as the change set.
3. If there are no uncommitted changes, run `git log --oneline -10` and show the recent commits.
4. Ask the user: "No uncommitted changes found. How many recent commits should I review against this plan?"
5. Default to the last commit if the user does not specify.
6. Use `git diff <base>..HEAD` for the selected commit range, and `git show --stat <commits>` for context.

Store the full diff for analysis in later steps.

### Step 3 — Load spec, repo rules, and risk context

- Read repo instructions from `AGENTS.md` and note any constraints relevant to the changed area.
- Check the plan frontmatter for `related_docs`.
- If a spec path is listed, read that spec too.
- If no spec is linked, check whether matching spec exists at `docs/plans/[feature-name]_spec.md` by deriving feature name from the plan filename.
- If found, read it and note that it was inferred rather than explicitly linked.
- Read the plan's `## CFL Verification`, `## Implementation Checklist Summary`, and phase/step sections for context, but do not treat them as proof that risk is covered.

### Step 4 — Attack spec and plan drift

Compare the implementation diff against both intended behavior and described scope.

Check for silent drift in:

- API request or response shape
- validation behavior and error semantics
- persistence behavior
- user-visible states and empty/error/loading states
- ownership, permission, or authorization behavior
- edge-case handling called out in spec or plan
- implementation scope beyond what the plan steps describe

Flag each issue as one of:

- **Critical** — likely broken contract, wrong shipped behavior, unsafe permission/data effect, or major hidden scope change
- **Major** — likely rework, caller breakage, behavior surprise, or meaningful scope drift
- **Minor** — clarification-worthy drift with limited blast radius

### Step 5 — Attack hidden hard-stop changes

Explicitly inspect whether the implementation touched or implied any repo hard-stop area.

Check for:

- shared type changes in `packages/shared/`
- `API_ROUTES` changes
- Zod schema contract changes
- Prisma schema or migration changes
- auth/security changes
- new dependencies or package manifest changes

For each hard-stop area found:

- say whether the plan or spec acknowledged it
- say whether explicit user confirmation should have happened before implementation
- flag omission as issue if the change was hidden, understated, or bundled into ordinary implementation steps

### Step 6 — Attack false-green validation

Review whether the available validation is strong enough for the actual risk of the change.

Inspect:

- `## CFL Verification` items in the plan
- tests added or updated in the diff
- missing tests for likely failure paths
- whether validation covers integration boundaries, not just local units
- whether frontend/backend contract changes were actually exercised
- whether old data, retries, duplicate submits, or unhappy paths were tested when relevant

Flag issues such as:

- validation that proves only happy path
- tests that mock away the risky integration point
- missing checks for regressions in reused callers
- checked-off CFL items that are too weak for the implemented change

### Step 7 — Attack regression surface and rollout risk

Use `Grep`, `Glob`, and `Read` to inspect the surrounding codebase and identify what existing flows may break.

Check for:

- shared helpers or types with many callers
- existing routes, services, hooks, or components that depend on changed behavior
- backend/frontend mismatch windows
- old data compatibility gaps
- migration, backfill, or deploy-order risk
- silent-failure paths with weak logging or observability

Do not require every topic for every change.
Only flag what is necessary for the actual risk profile.

### Step 8 — Attack literal code paths and architecture honesty

Read changed code as executed, not as intended.

Look for:

- null or undefined holes
- stale state or race conditions
- retries or duplicate submissions causing inconsistent state
- partial success without rollback or compensation
- skipped backend layers or business logic in controllers
- generic `Error`, `console.log`, duplicated types, or ad hoc patterns that create future fragility
- unplanned behavior changes that no spec requirement or plan step covers

Use existing code patterns in the repo as comparison points when necessary.

### Step 9 — Present adversarial review report

Output review in this format:

```markdown
## Adversarial Implementation Review: [plan name]

### Summary

[1-3 sentences. State whether implementation is safe to ship, risky, or not safe.]

### Critical Findings

- [file:line] — [why this can break behavior, contract, data, security, or rollout]

### Major Findings

- [file:line] — [impact]

### Minor Findings

- [file:line] — [impact]

### Spec/Plan Drift

- [what spec/plan says] vs [what implementation does]

### Hidden Hard-Stop Changes

- [area] — [what changed, whether it was acknowledged, whether confirmation should have happened]

### Regression Risks

- [existing flow or caller] — [why likely affected]

### Weak Validation / False-Green Risks

- [missing or weak check] — [why current validation may miss the problem]

### Rollout / Data Risks

- [migration, stale client, old data, partial deploy issue] — [impact]

### Literal Code-Path Risks

- [specific branch or flow] — [what likely goes wrong in execution]

### Unplanned Scope

- [extra behavior or change] — [why it matters]

### Verdict

**>> [SELECTED VERDICT] <<**

Choose exactly one and replace line above with it:

- `>> SAFE TO SHIP <<`
- `>> FIX BEFORE ARCHIVE <<`
- `>> NOT SAFE — REWORK REQUIRED <<`
```

Rules for output:

- Omit empty sections.
- No praise padding.
- No style nits unless they change risk.
- Prefer concrete citations: file paths, line numbers, plan step names, route names, model names, and caller locations.
- Prioritize what can ship wrong over what is merely incomplete.
- If implementation differs from the plan but appears safer or better, still flag the drift.
- If implementation differs from the spec on user-visible behavior or API contract, treat that as high-severity unless clearly documented and approved.

### Step 10 — Record the review in the plan

Append an entry to the `## Review History` section of `[plan-path]`. If the section does not exist (e.g. older plan without the template), add it immediately before `## File Index`, or at the end of the file if `## File Index` is also absent.

Use the Edit tool to insert the following block immediately after the `## Review History` header line (and its HTML comment, if present), before any previous entries — most recent review first:

```markdown
### Review — [YYYY-MM-DD] · [model-name] · adversarial

- **Verdict:** [one of: "SAFE TO SHIP" / "FIX BEFORE ARCHIVE" / "NOT SAFE — REWORK REQUIRED"]
- **Findings:** [N critical, N major, N minor — or "None"]
- **Fixes applied:** [findings from the previous review entry that are no longer present, or "N/A — first review" / "None — issues remain open"]
```

Derive each field from the Step 9 report:

- **Date:** use today's date from the system context
- **Model:** use the model currently running this skill
- **Verdict:** map directly from the Verdict line selected in Step 9
- **Findings:** count from the Critical, Major, and Minor sections of the Step 9 report
- **Fixes applied:** compare against the previous review entry (if one exists) — list findings from that entry that are no longer present in the current review; if this is the first review, write "N/A — first review"

Never touch anything else in the plan file — no checklist edits, no status changes, no CFL updates, no archive moves.
