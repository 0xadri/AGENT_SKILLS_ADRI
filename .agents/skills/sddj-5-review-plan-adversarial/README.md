# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Adversarial Review Plan Skill.

Read more in [SDDJ_INTRO.md](../sddj-0-create-spec/SDDJ_INTRO.md)

# FAQ: Adversarial Review Plan Skill

- What? -> Adversarial reviews plan doc.

- When? -> Skill to run after `/sddj-4-review-plan`

- Mandatory to run? -> No. Can be skipped. But highly recommended.

- Will it do any code change? -> No. Never.

## Workflow

1. Run skill along with file path to plan doc such as:

```bash
/sddj-5-review-plan-adversarial [path_to_plan]
```

2. Address all points raised by the review **marked as critical**.

3. Move to next SDDJ Skill

## Follow Up Prompts

When addressing issues found, if for whatever reason you need to create a new session, you may use the below prompt.

```bash
I got the below critic during a review about [path_to_plan]. Can you expand and explain?

[critic_here]
```
