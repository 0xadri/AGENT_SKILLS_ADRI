# Readme For Humans

This guide is for humans only, to help you dear human being.

> [!WARNING]
> This agent skill is early stage.
> But it's so good I wanted to share it anyway.
> It will be updated, polished and enriched.

---

# Why Use This Skill?

Try many possible UX solutions without the heavy lifting of production-grade code:

- no DB connections
- no tests
- no error handling
- no polish
- and so on

Benefits:

- calibrate the change to your liking: `Macro`, `Meso`, `Micro`, or `Custom`
- iterate fast until happy
- get tons of inspiration "for free"

Additional Benefits:

- complex UX becomes easy to build
- spend 10x less token than when one-shotting with production-grade code
- avoid re-working the UX 10 times after production-grade code was implemented

---

# Recommended Workflow

1. Use Agent Skill To Create Many Variants - get creative with colors, layout, sizes, animations, etc
2. Use Prompt To Create One More Variant - this one combines your favorite parts of the others created before; polish it until happy
3. Use Prompt To Extract Specs, And Backup Code - and optionally take screenshots (possibly screencast)
4. Use Prompt And Specs To Create Production Ready Code - do it in a new session
5. Delete prototype code

---

# Details For Each Step

## 1. Use This Agent Skill To Create Many Variants

Prompt Might Look Like:

```markdown
`/exe-prototype-ux` 12 solutions
user edit page is clunky it has too much content lets break it down in several parts (i.e. tabs, accordions, or else) to improve the UX, but of course it is welcome to play around with layout, sizes, animations, and any relevant UX patterns relevant to our problem and goal
`packages/frontend/src/pages/UserEdit`
```

## 2. Use Prompt To Create One More Variant

Prompt Might Look Like:

```markdown
Create one more variant that combines:

- the top tabs layout of solution 12
- the color scheme of solution 6
- the title styling of solution 3

Watch out:

- Update switcher
- Do not write tests.
- Run typecheck and lint to catch issues introduced by variant and switcher. Only fix errors introduced in changed files.
```

## 3. Use Prompt To Extract Specs, And Backup Code

"Extract Specs And Backup Code" Prompt Might Look Like:

```markdown
For solution 13 only:

- Extract a detailed spec of the UX so that it can be used later to create production ready implementation (with all the bells and whistles such as appropriate tests).
- In that same spec, embed a very succinct backup of the solution as code in a fenced code block.
- This code backup must be self contained.
- The spec must outlive the prototype: NEVER reference prototype files or anything created during prototyping — they will be deleted.
- Referencing permanent project files is fine.
- Put it all under `docs/plans/tmp_prototype_ux.md`
```

## 4. Delete Throwaway Code

Delete all files created in previous steps except `docs/plans/tmp_prototype_ux.md`

## 5. Rename Specs And Code

Optional - useful if you want to commit it.

Prompt Might Look Like:

```markdown
Rename `docs/plans/tmp_prototype_ux.md` to `docs/plans/FEATURE_NAME_spec.md`
```

## 6. Use Prompt And Specs To Create Production Ready Code

Prompt Might Look Like:

```markdown
`docs/plans/tmp_prototype_ux.md` is the extracted specs of a UX prototype implemented with throwaway code.
Implement the production ready code following this project's code guidelines and best practices such as tests, error handling, abstractions. Look at AGENTS.md for more.
If the spec references prototype code, treat it as informative only — throwaway code, not a model to copy. Rebuild properly.
```

Or if you want to first do a plan:

```markdown
`docs/plans/tmp_prototype_ux.md` is the extracted specs of a UX prototype implemented with throwaway code.
Create a plan for it.
If the spec references prototype code, treat it as informative only — throwaway code, not a model to copy. Rebuild properly.
```
