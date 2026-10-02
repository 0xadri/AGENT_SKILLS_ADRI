# SDD Jazz

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Create Plan Skill.

Read more in [SDDJ_INTRO.md](../sddj-0-create-spec/SDDJ_INTRO.md)

# FAQ: Adversarial Review Spec Skill

- What? -> Adversarial review the spec doc. One the SDDJ skills.

- When? -> Skill to run after `/sddj-1-review-spec`

- Mandatory to run? -> No. Can be skipped. But highly recommended.

- Will it do any code change? -> No. Never.

# Skill: Create Plan

What? Creates plan doc from spec doc.

Mandatory? Yes.

## Workflow

1. Run skill along with description such as: `/sddj-3-create-plan [path_to_spec]`

2. Answer any questions the LLM asks you.

3. Read the plan doc that was created. At least have a quick look.

4. Move to next SDDJ Skill
