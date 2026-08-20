WARNING: This repo is super early stage

# Intro

This repo is for agent skills created by me (Adri).

# My Motto

1. Use CLIs over IDEs.
   IDEs are not anywhere near in term of customization, quality and performance.
2. Be CLI/IDE agnostic.
   It can be difficult to switch CLI, take that habit early to make sure you don't get locked in.
3. Avoid closed CLIs, favor OpenCode and Pi.
4. Build agent skills that are CLI agnostic.
   Watch out: the lock-ins only come from the CLIs, not the LLMs
5. Try agent skills from others.
   To get started, or for exploration & discovery purpose
6. Use the right LLM for the job.
   Use several LLMs for diversity of opinions and expertise.

# Why Use Agent Skills

Agent Skills are great to achieve consistency.

Other benefits include: a task will be achieve faster by the LLM, hence cheaper, and using less context window (potentially less compacting).

# When To Use Agent Skills

Situations in which you may want to create an agent skill:

- a 1-5 lines long prompt you run 3+ times per week, you want a shortcut
- a 6+ lines long prompt you run 1+ time per week, you want 1/ a shortcut 2/ to iterate on it to make it more efficient 3/ achieve consistency across runs

# Best Practices When Creating Agent Skills

- Start small - your ego is not your amigo
- Build many tiny skills
- Keep instructions concise - consider using caveman-like tool
- Agents are verbose, if an agent helps you create the skill, instruct it to "make the smallest possible change"
- Grow slowly - use the skill a lot, build trust and experience, and only when it feels mature and stable, then iterate
- KISS: Provide the least paths possible - this may confuse the agent
- KISS: Provide the least loops possible
- Guide the user as much as possible - highlight important info in tiny table of 1 cell if needed
- Only use name and description in the frontmatter -> so it's supported across harnesses/CLIs

# Official Specs For Agent Skills

- Agent Skills Open Standard - https://agentskills.io/
- Agent Skills Open Standard: Specs - https://agentskills.io/specification
- Claude Code CLI: Agent Skills - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- OpenCode: Agent Skills - https://opencode.ai/docs/skills/
- Copilot: Agent Skills - https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- Codex: Agent Skills - https://developers.openai.com/codex/skills

# Shout Out

Shout out to all individuals and teams making their agent skills public such as Vercel, Cursor, Anthropic, Matt Pocock, Kun Chen, Addy Osmani, Theo (t3dotgg), Affaan Mustafa, and others
