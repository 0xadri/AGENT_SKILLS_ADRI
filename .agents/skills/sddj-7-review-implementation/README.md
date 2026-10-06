# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Review Implementation Skill.

Read more in [SDDJ_INTRO.md](../sddj-0-create-spec/SDDJ_INTRO.md)

# FAQ: Review Implementation Skill

## When?

Skill to run after `/sddj-6-implement-plan`

## What?

Reviews code changes against an implementation plan — checks plan compliance, CFL verification, and checklist status.

## Mandatory to run?

No. Can be skipped.

## Will it do any code change?

No.

# Workflow: Review Implementation Skill

1. Run skill along with file path to plan doc such as:

```bash
/sddj-7-review-implementation [path_to_plan]
```

2. Address all points raised by the review **marked as critical**.

## Follow Up Prompts

When addressing issues found, if for whatever reason you need to create a new session, you may use the below prompt.

```bash
I got the below critic during a code review of our implementation of [path_to_plan]. Can you expand and explain?

[critic_here]
```
