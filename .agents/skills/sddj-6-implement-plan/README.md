# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Implement Plan Skill.

Read more in [SDDJ_INTRO.md](../sddj-0-create-spec/SDDJ_INTRO.md)

# FAQ: Implement Plan Skill

## When?

Skill to run after `/sddj-5-review-plan-adversarial`

## What?

Implements a plan document step by step, sync status across plan and spec, and archive both when complete.

## Mandatory to run?

Yes.

## Will it do any code change?

Yes. Many.

# Workflow: Implement Plan Skill

1. Run skill along with file path to plan doc such as:

```bash
/sddj-6-implement-plan [path_to_plan]
```

2. Answer any question the LLM asks during the implementation

3. Have a look at your change on your app. Voila!

4. Move to next SDDJ Skill
