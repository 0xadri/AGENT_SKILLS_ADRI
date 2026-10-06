# Readme For Humans

This guide is for humans only, to help you dear human being.

# Why Use This Skill?

Skill exists. Slash missing. Expose fast:

- verifies `.agents/skills/<skill>/SKILL.md` exists
- creates `.opencode/commands/<skill>.md` wrapper
- wrapper loads skill, passes `$ARGUMENTS`
- one file, no other edits

# FAQ: Wrap Skill As Opencode Command Skill

## When?

New skill done. Want `/skill` shortcut in opencode.

## What?

Creates matching opencode command wrapper for existing skill.

## Will it do any code change?

Yes. Creates one file: `.opencode/commands/<skill>.md`. Nothing else.

## Example Prompt

```markdown
/meta-wrap-skill-as-opencode-command get-matrix-table
```
