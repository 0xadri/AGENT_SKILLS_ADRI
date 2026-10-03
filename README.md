<p align="center">
  <strong>AGENT_SKILLS_ADRI</strong>
</p>

# Intro

This repo is for agent skills created by Adri.

See them all in directory [.agents/skills](./.agents/skills)

> [!WARNING]
> This repo is super early stage. It will be updated, polished and enriched.
> That being said, the skills currently shared are solid. More below.

## Quick Start

Download the repo as a zip file. Open the zip. Copy/Paste files and directories to the relevant place in your project.

---

# Skills

| Skill                                 | Description                                                                                            |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `exe-caveman-style`                   | Terse, high-signal compressed prose — caveman-basic tone, remove filler, keep technical wording exact. |
| `exe-prototype-ux`                    | Build a throwaway UX prototype with N switchable variants to answer a design question.                 |
| `get-handoff-prompt`                  | Compact the current conversation into a handoff prompt for another agent to pick up.                   |
| `get-response-as-markdown`            | Format and copy the last response as markdown (prettify tables, spacing) to clipboard.                 |
| `meta-wrap-skill-as-opencode-command` | Create a `.opencode/commands/<skill>.md` slash-command wrapper for an existing skill.                  |
| `ref-input-txt-phone-guardrails`      | Phone number input validation and review guardrails for forms, APIs, and contact flows.                |
| `sddj-0-create-spec`                  | SDDJ: Create spec for new feature                                                                      |
| `sddj-1-review-spec`                  | SDDJ: Review a drafted spec                                                                            |
| `sddj-2-review-spec-adversarial`      | SDDJ: Review spec adversarially                                                                        |
| `sddj-3-create-plan`                  | SDDJ: Create an implementation plan from a spec document.                                              |
| `sddj-4-review-plan`                  | SDDJ: Review a drafted implementation plan                                                             |
| `sddj-5-review-plan-adversarial`      | SDDJ: Review implementation plan adversarially                                                         |
| `sddj-6-implement-plan`               | SDDJ: Implement a plan document                                                                        |
| `sddj-7-review-implementation`        | SDDJ: Review code changes against an implementation plan                                               |
| `sddj-frontmatter`                    | SDDJ: Add/Update YAML frontmatter to doc files with metadata fields                                    |
| `sddj-imp-status`                     | SDDJ: Add/Update implementation status to a document.                                                  |
| `sddj-read-time`                      | SDDJ: Add/Update reading time to a document.                                                           |
| `sddj-table-of-contents`              | SDDJ: Add/Update a linked Table of Contents section to a document.                                     |

## Skills Naming Convention (prefix)

- `get-*` = read-only skills
- `exe-*` = read-write skills
- `meta-*` = skills about skills
- `ref-*` = reference implementations
- `sddj-*` = skill part of SDDJ framework

---

# FAQ

## Supported CLIs?

All. These agents skills are built to be CLI agnostic.

## How Mature Are These Agent Skills?

They are usually battle tested. Typically used at least a dozen times.

However, each skill has a README with a note if it is still early stage.

## What CLI Did You Use Them Most With?

OpenCode.

## Will You Release More Skills?

Yes. This is just a preview.

## Will You Release A Plugin?

Maybe.

## Can I Read More About Skills?

Yes, in [README_MORE.md](README_MORE.md)

## Anything else AI related you published?

Yes. My AGENTS.md repo [AGENTS.md_ADRI](https://github.com/0xadri/AGENTS.md_ADRI)

---

# Shout Out

Shout out to all individuals, teams and companies making their agent skills public such as Vercel, Cursor, Anthropic, Matt Pocock, Kun Chen, Addy Osmani, Theo (t3dotgg), Affaan Mustafa, and others.

---

# License

Made with ♥ by [@0xadri](https://github.com/0xadri) and released under the MIT license.
