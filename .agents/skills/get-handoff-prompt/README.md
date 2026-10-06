# Readme For Humans

This guide is for humans only, to help you dear human being.

# Why Use This Skill?

Context full. Work not done. No re-explain from zero:

- compacts convo into handoff prompt for fresh session
- keeps state, decisions, next steps
- suggests skills for next agent
- refs specs, plans, commits by path, no copy-paste bloat
- redacts secrets, keys, PII

# FAQ: Handoff Prompt Skill

## When?

Context low. Switch agent. Pause, resume later.

## What?

Outputs handoff prompt. Fresh agent picks up where you left.

## Will it do any code change?

No. Never. Output to chat only.

## Example Prompt

Run with focus for next session, such as:

```markdown
/get-handoff-prompt continue cool_feature fix, specs in docs/plans/cool_feature_spec.md

/get-handoff-prompt review plan adversarially next
```
