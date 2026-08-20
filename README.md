WARNING: This repo is super early stage

# Intro

This repo is for agent skills created by me (Adri).

# My Motto

1. Be CLI/IDE agnostic: value is in the LLM, not the CLI
2. Avoid closed CLIs, favor OpenCode and Pi
3. Use the right LLM for the job.
   Use several LLMs for diversity of opinion.
4. Build agent skills that are CLI agnostic.
   Watch out: the lock-ins seem to only come from the CLIs, not the LLMs

# Why Use Agent Skills

Agent Skills are great to achieve consistency.

# When To Use Agent Skills

Situations in which you may want to create an agent skill:

- a 1-5 lines long prompt you run 3+ times per week, you want a shortcut
- a 6+ lines long prompt you run 1+ time per week, you want 1/ a shortcut 2/ to iterate on it to make it more efficient 3/ achieve consistency across runs

# Best Practices When Creating Agent Skills

- Start small - your ego is not your amigo
- Build many tiny skills
- Grow slowly - use the skill a lot, build trust and experience, and only when it feels mature and stable, then iterate
- Keep instructions concise - consider using caveman-like tool
- Agents are verbose, if an agent helps you create the skill, instruct it to "make the smallest possible change"
- Provide the least paths possible - this may confuse the agent
- Provide the least loops possible
- Guide the user as much as possible - highlight important info in tiny table of 1 cell if needed
- Only use name and description in the frontmatter -> so it's supported across harnesses

# Official Specs For Agent Skills

- Agent Skills Open Standard - https://agentskills.io/
- Agent Skills Open Standard: Specs - https://agentskills.io/specification
- Claude Code CLI: Agent Skills - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- OpenCode: Agent Skills - https://opencode.ai/docs/skills/
- Copilot: Agent Skills - https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- Codex: Agent Skills - https://developers.openai.com/codex/skills

# Shout Out

Shout out to all individuals and teams making their agent skills public such as Vercel, Cursor, Anthropic, Matt Pocock, Kun Chen, Addy Osmani, Theo (t3dotgg), Affaan Mustafa, and others
