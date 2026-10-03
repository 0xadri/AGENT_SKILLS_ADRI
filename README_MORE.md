# Best Practices, Good Habits, and Opinions

Gist of some of my key learnings.

# CLIs, IDEs, and LLMs

1. Use CLIs over IDEs.
   IDEs are not anywhere near in term of customization, quality and performance.
2. Be CLI agnostic.
   It can be difficult to switch CLI, take that habit early to make sure you don't get locked in.
3. Avoid closed CLIs, favor OpenCode and Pi.
4. Try agent skills from others.
   To get started, or for exploration & discovery purposes.
5. Build agent skills that are CLI agnostic.
   Watch out: lock-ins only come from the CLIs, not the LLMs.
6. Use several LLMs for diversity of opinions and expertise.
   Once you know what LLM is good for what, use the right LLM for the job.

# Why Use Agent Skills

Agent Skills are great to achieve consistency.

Think of them as sticky notes to remind your LLM how to do a task correctly and efficiently.

Other benefits include:

- a task will be achieved faster by the LLM, hence cheaper, hence using less context window, hence potentially less compacting.
- great to develop a feeling, and a trustworthy experience about models. You run skills very often, so given a specific task you get to notice which models fail and which succeed.

# When To Use Agent Skills

Situations in which you may want to create an agent skill:

- a 40-200 words long prompt you run 3+ times per week, you want a shortcut.
- a 240+ words long prompt you run 1+ time per week, you want 1/ a shortcut and 2/ to iterate on it to refine it and 3/ to achieve consistency across runs

# Best Practices When Creating Agent Skills

- Start small - your ego is not your amigo
- Build many tiny skills
- Keep instructions concise - consider using a caveman-like tool
- Agents are verbose, if an agent helps you create the skill, instruct it to "make the smallest possible change"
- Grow slowly - use the skill a lot, build trust and experience, and only when it feels mature and stable, then iterate
- KISS: Provide the least paths possible - this may confuse the agent
- KISS: Provide the least loops possible
- Guide the user as much as possible - highlight important info in tiny table of 1 cell if needed
- Only use name and description in the frontmatter -> so it's supported across harnesses/CLIs

Frontmatter:

- Only use "name" and "description". The others are not cross-harness.
- Name: add prefix of 3-4 chars to avoid conflicts and group skills by topic i.e. `cfl-` for `close-feedback-loop` (gate related skills), `sdd-` for what you know, `exe-` for skills that change files, `get-` for skills that are read-only, and so on.
- Description: that's loaded in the cache of your cli/harness, it should be brief so your cli/harness don't get too bloated, it should have key words that should trigger an implicit call of the skill.

# Official Specs For Agent Skills

- Agent Skills **Open Standard** - https://agentskills.io/
- `Claude Code CLI`: Agent Skills - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- `OpenCode`: Agent Skills - https://opencode.ai/docs/skills/
- `Codex`: Agent Skills - https://developers.openai.com/codex/skills
- `Copilot`: Agent Skills - https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
