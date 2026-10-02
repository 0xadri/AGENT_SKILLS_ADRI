# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Review Implementation Skill.

Read more in [SDDJ_INTRO.md](../sddj-0-create-spec/SDDJ_INTRO.md)

# Skill: Review Of Implementation

What? Reviews the code implementation of a given plan doc.

Mandatory? No. Can be skipped. But recommended.

## Workflow

1. Run skill along with file path to plan doc such as:

```bash
/sddj-7-review-implementation [path_to_plan]
```

2. Address all points raised by the review **marked as critical**.

3. Move to next SDDJ Skill

## Follow Up Prompts

When addressing issues found, if for whatever reason you need to create a new session, you may use the below prompt.

```bash
I got the below critic during a code review of our implementation of [path_to_plan]. Can you expand and explain?

[critic_here]
```
