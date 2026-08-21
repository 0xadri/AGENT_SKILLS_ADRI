WARNING: This repo is super early stage

# Intro

This repo is for agent skills created by me (Adri).

See them all in directory [.agents](./.agents)

---

# FAQ

## Supported CLIs?

All. These agents skills are built to be CLI agnostic.

## How Mature Are These Skills?

They are battle tested. Usually used at least a dozen times before being shared.

## What CLI Did You Use Them Most With?

OpenCode.

## Will You Release More Skills?

Yes. This is just a preview.

## Will You Release A Plugin?

Maybe.

## How Do I Use These?

Download the repo as a zip file. Open the zip. Copy/Paste files and directories to the relevant place in your project.

---

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

Other benefits include:

- a task will be achieve faster by the LLM, hence cheaper, and using less context window (potentially less compacting).
- great to develop a feeling about models, you run them very often, so given a specific task you get to notice which LLMs fail and which succeed.

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

Frontmatter:

- Only use "name" and "description". The others are not cross-harness.
- Name: add prefix of 3-4 chars to avoid conflicts and group skills by topic i.e. `cfl-` for `close-feedback-loop` (gate related skills), `sdd-` for what you know, `exe-` for skills that change files, `get-` for skills that are read-only, and so on.
- Description: that's loaded in the cache of your cli/harness, it should be brief so your cli/harness don't get too bloated, it should have key words that should trigger an implicit call of the skill.

# Official Specs For Agent Skills

- Agent Skills Open Standard - https://agentskills.io/
- Agent Skills Open Standard: Specs - https://agentskills.io/specification
- Claude Code CLI: Agent Skills - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- OpenCode: Agent Skills - https://opencode.ai/docs/skills/
- Copilot: Agent Skills - https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- Codex: Agent Skills - https://developers.openai.com/codex/skills

# Shout Out

Shout out to all individuals and teams making their agent skills public such as Vercel, Cursor, Anthropic, Matt Pocock, Kun Chen, Addy Osmani, Theo (t3dotgg), Affaan Mustafa, and others

---

# You Might Also Like

My AGENTS.md repo [AGENTS.md_ADRI](https://github.com/0xadri/AGENTS.md_ADRI)
