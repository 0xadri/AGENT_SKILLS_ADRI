---
name: get-handoff-prompt
description: Compact the current conversation into a handoff prompt for another agent to pick up.
---

We're running out of context.

Output a handoff prompt summarising the current conversation so an agent with a fresh new session can continue the work from where we are.

Include a "suggested skills" section in the document, which suggests skills that the agent should invoke.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.
