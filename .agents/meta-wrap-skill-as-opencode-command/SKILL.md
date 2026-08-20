---
name: meta-wrap-skill-as-opencode-command
description: Create a matching `.opencode/commands/<skill>.md` wrapper for an existing `.agents/skills/<skill>/SKILL.md`. Use when asked to expose a skill as an opencode slash command.
---

If `$1` is missing, ask for the skill name.
Verify `.agents/skills/$1/SKILL.md` exists.
Create `.opencode/commands/$1.md` with:

```md
---
description: Run the $1 skill
---

Load the `$1` skill and follow it exactly.

Arguments: `$ARGUMENTS`
```

Report the created path.
