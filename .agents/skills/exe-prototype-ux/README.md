# Readme For Humans

This guide is for humans only, to help you dear human being.

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

```bash
/exe-prototype-ux 12 solutions
user edit page is clunky it has too much content lets break it down in several parts (i.e. tabs, accordions, or else) to improve the UX, but of course it is welcome to play around with layout, sizes, animations, and any relevant UX patterns relevant to our problem and goal
packages/frontend/src/pages/UserEdit
```

## 2. Use Prompt To Create One More Variant

Prompt Might Look Like:

```bash
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

"Backup Code" Prompt Might Look Like:

```bash
For solution 13 only:
Make a very succint backup of the solution as code.
NEVER reference other files.
Put it under docs/plans/tmp_prototype_ux.tsx
```

"Extract Specs" Prompt Might Look Like:

```bash
For solution 13 only:
Extract a detailed spec of the UX so that it can be used later to create production ready implementation (with all the bells and whistles such as appropriate tests).
Put this spec under docs/plans/tmp_prototype_ux.md
Reference the code backup docs/plans/tmp_prototype_ux.tsx
NEVER reference other files though.
```

## 4. Delete Throwaway Code

Delete all files created in previous steps except:

- `docs/plans/tmp_prototype_ux.md`
- `docs/plans/tmp_prototype_ux.tsx`

## 5. Rename Specs And Code

This is optional - useful if you want to keep these files in the repo for the record.

Prompt Might Look Like:

```bash
Rename `docs/plans/tmp_prototype_ux.md` to `docs/plans/FEATURE_NAME_spec.md`
Rename `docs/plans/tmp_prototype_ux.tsx` to `docs/plans/FEATURE_NAME_prototype_code.tsx`
```

## 6. Use Prompt And Specs To Create Production Ready Code

Prompt Might Look Like:

```bash
docs/plans/tmp_prototype_ux.md is the extracted specs of a UX prototype implemented with throwaway code.
Implement the production ready code following this project's code guidelines and best practices such as tests, error handling, abstractions. Look at AGENTS.md for more.
If the spec references prototype code (e.g. docs/plans/tmp_prototype_ux.tsx), treat it as informative only — throwaway code, not a model to copy. Rebuild properly.
```

Or if you want to first do a plan:

```bash
docs/plans/tmp_prototype_ux.md is the extracted specs of a UX prototype implemented with throwaway code.
Create a plan for it.
If the spec references prototype code (e.g. docs/plans/tmp_prototype_ux.tsx), treat it as informative only — throwaway code, not a model to copy. Rebuild properly.
```
