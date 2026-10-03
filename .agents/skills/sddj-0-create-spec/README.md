# SDD Jazz

> [!WARNING]
> This framework is battle tested. However, the doc is early stage.
> More coming soon. Keep an eye on this page.

SDDJ stands for SDD Jazz.

SDDJ is a SDD framework.

SDDJ has many skills, including this very Create Spec Skill.

Read more in [SDDJ_INTRO.md](./SDDJ_INTRO.md)

# FAQ: Create Spec Skill

- When? -> 1st skill to run when working with SSDJ.

- What? -> Creates the spec doc.

- Mandatory to run? -> Yes.

- Will it do any code change? -> No. Never.

## Workflow

1. Run this skill along with description such as:

```markdown
/sddj-0-create-spec [description]
```

2. Answer all questions the LLM asks you.

3. Resolve everything in "Open Questions" section of the created spec doc
   - if needed ask the LLM for further explanations so you can take the best possible decisions
   - if you defer something, remember to add it to Future Enhancement section

4. Read the entire spec document that was created, as a "manual review" step.

5. Move to next SDDJ Skill
