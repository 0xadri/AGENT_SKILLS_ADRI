# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDD stands for Specs Driven Development.

# Principles

SDD Jazz has an opinionated behavior over configurability.

Think:

- tiny library that get the job done over fancy do-it-all framework
- low tech that actually works

# FAQ

## Can I know more about SDD?

Read more -> [SDD_INTRO.md](.agents/skills/sddj-0-create-spec/SDD_INTRO.md)

## Why are the skills numbers `sddj-0-*`, `sddj-1-*`, and so on?

Because that's the typical order in which you run them.

_All the other skills that do not have a number in their name are subskills used by the other skills, you usually do not call them individually but you can if needed._

## What do devs wish they knew first time they used SDDJ?

It first calibrates the plan depth according to your preference.

It enforces consistency between every plan, i.e. frontmatter, sections, topics, format, and more.

**Additional Wins:**

- Documents for future devs and future you, i.e. complex decisions
- Docs can be shared and/or approved by colleagues
  - spec docs → for technical and non-technical colleagues
  - implementation plan → for technical colleagues

## Any rules of thumb?

- If your spec doc is over 10 thousand words, there is 99% chance your change is too big, split it
- Use you favorite strongest LLM to build the spec doc, and ideally also when building the plan doc. See below why:
  - If your spec doc has mistakes, they will all trickle down into the plan and implementation, triple check that doc, make it rock solid
  - If your plan doc has mistakes, they will all trickle down into the implementation

## What Design Principles does it follow?

- Minimize User Confusion — a simple workflow
- Minimize Token Usage (1) — user chooses the calibre of workflow according to change size
- Minimize Token Usage (2) — each skill is small, under 400 lines
- Minimize Hallucination — each skill is as constraining/specific as possible, although concise
- Separate Audiences — spec is as high level as possible, plan goes into details and actual code
- Convention Over Configuration — file names patterns and directory structure

### Can you elaborate on "Convention Over Configuration"?

This is for information only. The below all comes out of the box, the skills know this, you do not need to do anything.

This framework always uses both a spec file AND a plan file.

File Names Patterns

- `[feature-name]_spec.md` — spec files
- `[feature-name]_plan.md` — plan files

Directory Structure

- `./docs/plans` — directory for **Work-In-Progress** specs and plans
- `./docs/plans_done` — directory for **Snapshots** specs and plans; archive records of past/fully-implemented tasks

Example

Let's say you are working on two tasks (one still in spec-ing phase) and you already fully implemented 3 others. It'll look something like this:

```markdown
./docs/plans
├── dark-mode_spec.md
├── dark-mode_plan.md
└── search-feature_spec.md

./docs/plans_done
├── user-authentication_spec.md
├── user-authentication_plan.md
├── password-reset_spec.md
├── password-reset_plan.md
├── email-notifications_spec.md
└── email-notifications_plan.md
```

## You say `./docs/plans_done` is for archive purpose but what stops my LLM from modifying content?

Good catch. Actually I strongly advise to add this line to your `AGENTS.md` (or `CLAUDE.md`).

```markdown
- NEVER edit or delete any existing files in `docs/plans_done/` directory. Creating or adding new files is allowed.
```

## What if you first work on a UX Prototype, do you include it in the spec or plan?

I usually create a companion doc such as `docs/plans/[FEATURE_NAME]\_prototype_ux.md`

This doc is self contained, it includes both specs and code.

I do this using skill `exe-prototype-ux`.

## What if you have manual steps to do (i.e. DevOps tasks), do you include them in the plan?

I usually create a companion plan such as `docs/plans/[FEATURE_NAME]\_manual_steps_plan.md`
