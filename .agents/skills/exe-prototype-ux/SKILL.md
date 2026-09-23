---
name: exe-prototype-ux
description: Build a throwaway UX prototype to answer a design question, with N switchable variants. Use when the user wants to explore what a UI should look like.
---

# UX Prototype

A prototype is **throwaway code that answers a question**.
The point is to learn something fast.

## Goal

Generate **calibrated UI variations** (Macro, Meso, Micro, or Custom) on a single route, switchable from a floating bottom bar.
The user flips between variants in the browser, picks one (or steals bits from each), then throws the rest away.

## Rules

1. **Skip the polish — NEVER write tests.**
   No error handling beyond what makes the prototype _runnable_, no abstractions.
   The prototype dies once the question is answered; tests would die with it.
2. **Throwaway from day one, and clearly marked as such.**
   Locate the prototype code close to where it will actually be used (next to the module or page it's prototyping for) so context is obvious.
   Name prototype files and add comments so a casual reader can easily see it's a prototype, not production code.
   For throwaway UI routes, obey whatever routing convention the project already uses; don't invent a new top-level structure.
3. **NEVER commit this code.**
   It's throwaway code so do consider using a throwaway branch.
4. **No persistence by default.**
   State lives in memory. Persistence is the thing the prototype is _checking_, not something it should depend on.
   If the question explicitly involves a database, hit a scratch DB or a local file with a clear "PROTOTYPE — wipe me" name.
5. **If prototype stays in existing page context.**
   Prototype works best inside real app context: real header, sidebar, data, density.
   Vacuum routes mislead.
   When route already exists, render variants on same route via in-memory switcher state.
   Keep data fetching, route params, and auth untouched.
   Only swap rendering.
   Variant 1 must always preserve exact original baseline.

## Process

### 1. State the question, pick N, and calibrate variance scope

**Parse `[N]` and tier keyword from `$ARGUMENTS`:**

Check whether `$ARGUMENTS` begins with a number (e.g. `3 redesign the settings page`).
If it does, extract that number as `[N]` and treat the remainder as the problem description.
If no leading number is present, default `[N]` to `10`.
`[N]` must be between 2 and 20.
If user provides a value outside that range, warn them and clamp to the nearest bound (2 or 20).

Scan `$ARGUMENTS` for optional variance tier keywords: `macro`, `meso`, `micro`.

**Calibrate variance scope via `AskUserQuestion`:**

ALWAYS confirm or ask calibration tier before writing code.
Never proceed without explicit confirmation.

- If keyword detected in `$ARGUMENTS`:
  Call `AskUserQuestion` confirming inferred scope.
  Recommend inferred tier.
  Allow user to confirm, pick a different tier, or provide additional focus instructions.
- If no keyword detected:
  Call `AskUserQuestion` asking user to calibrate variance scope.
  Recommend `Macro (Structural)` as default.

Offer options:

1. `Macro (Structural UX patterns) (Recommended)` — Radically different containers and workflows (tabs vs wizard vs master-detail vs drawer).
   Best for broad early exploration.
2. `Meso (Layout & Information Architecture)` — Same container, different density, hierarchy, and component types (cards vs table vs timeline).
   Best when container is fixed.
3. `Micro (Visual & Styling polish)` — Same layout, different typography, spacing, contrast, colors, micro-interactions.
   Best for polish passes.
4. `Custom focus` — User specifies exact axis to explore (e.g. only test search filters or mobile density).

With 7+ variants, similarity creep happens fast.
Assign each variant a distinct archetype matching the chosen calibration tier.

Write down the plan in one line, in the prototype's location or a top-of-file comment:

> "`[N]` [tier] variants of the settings page, switchable via the prototype switcher, on the existing `/settings` route."

This works whether the user is here to push back or not.

### 2. Guard the target file

Locate the page/route component the prototype adjusts, read the full file, then run three guards before writing anything:

**Non-component guard:**

Verify the file contains at least one JSX-returning function or component declaration.
If it's a hook (`use*.ts`), utility, constants, or types file with no JSX, stop and warn the user: "This file doesn't appear to be a React component — the variant switcher needs a JSX-returning component as the target. Point me at the component file instead."

**Multiple exported components guard:**

Count how many component exports the file contains.
If more than one (e.g. `SettingsPage` and `SettingsHeader`), list them and ask the user: "This file exports multiple components — which one should the switcher target?" Wait for selection.
Do not guess.

**Duplicate switcher guard:**

Check whether a previous run left structure behind — `Variant1`/`Variant2` or old `VariantA`/`VariantB`-style definitions or a rendered `PrototypeSwitcher` in the file.
If so, hold the full original file content in memory (read before any edits), then strip the old variant structure before rebuilding.
Rebuild from the original held in memory — not from the stripped file.
Variant 1 must be reconstructed from the original code held in memory.
Treat the stripped file as a clean slate.

### 3. Generate variants using calibrated UX tier

**Variant 1 — preserve original:**

Variant 1 must always be original version, reproduced exactly as baseline before edits.
User must never lose working baseline.
Name it `Variant1` with label `original` or `baseline`.

**Variants 2–`[N]` — alternative solutions:**

Draft remaining variants adhering strictly to the calibrated tier.
Hold each variant to:

- Page purpose and available data.
- Project component library or styling system (TailwindCSS, shadcn, MUI, plain CSS).
- Clear exported component name, e.g. `Variant2`, `Variant3`.
- Short descriptive name stating primary variation pattern (e.g. `tabs`, `dense-table`, `pill-filters`).

**Tier-specific variation axes:**

- **Macro (Structural UX patterns):**
  Vary container and core workflow fundamentally.
  Do not rely on cosmetic tweaks.
  Containers: Tabs, accordions, master-detail split, sidebar navigation, multi-step wizard, slide-over drawer, modal sheet.
  Hierarchy: Progressive disclosure, summary dashboard first, dense data table, visual cards, timeline stream.
  Workflow: Inline editing, sticky action bar, contextual popover, command palette, search or filter first.
- **Meso (Layout & Information Architecture):**
  Keep outer page container fixed.
  Vary information hierarchy, data presentation density, and component choices inside the container.
  Examples: Cards vs table vs compact list, filter drawer vs top bar vs facet chips, summary metric banners vs full inline feeds.
- **Micro (Visual & Styling polish):**
  Keep container, layout, and component types fixed.
  Vary visual styling and micro-interactions.
  Examples: Spacing density (compact vs spacious), border radius and elevation, typography scale and weight contrast, accents and theme tones, button styles and placements.
- **Custom focus:**
  Follow user-specified axis strictly across all variants.

### 4. Wire them together

Create a single switcher component on the route:

```tsx
// pseudo-code — adapt to the project's framework
const [variant, setVariant] = useState(1);
return (
  <>
    {variant === 1 && <Variant1 {...data} />}
    {variant === 2 && <Variant2 {...data} />}
    {variant === 3 && <Variant3 {...data} />}
    <PrototypeSwitcher
      variants={[
        { id: 1, name: "original" },
        { id: 2, name: "left sidebar" },
        { id: 3, name: "chaos layout" },
      ]}
      current={variant}
      onCycle={setVariant}
    />
  </>
);
```

Keep all the existing data fetching above the switcher; only the rendered subtree changes per variant.

### 5. Build the floating switcher

A small fixed-position bar at the bottom-centre of the screen with three pieces:

- **Left arrow** — cycles to the previous variant (wraps around).
- **Variant label** — shows variant number and descriptive name.
  NEVER use letters to identify the version.
  ALWAYS use numbers such as "1/10 original", "2/10 left sidebar", or "4/10 chaos layout".
  Format as `<current>/<total> <name>`.
- **Right arrow** — cycles forward (wraps around).

Behaviour:

- Clicking an arrow cycles the variant in state (wraps around).
  No URL changes — the variant is not shareable or reload-stable, and that's fine for a throwaway.
- Fixed width and fixed height.
  The bar must never grow, shrink, or shift when the variant label changes length — anchor it with explicit dimensions, not content size.
- Visually distinct from the page (e.g. high-contrast pill, subtle shadow) so it's obviously not part of the design being evaluated.
- Hidden in production builds — gate on `process.env.NODE_ENV !== 'production'` or an equivalent check, so a stray prototype merge can't ship the bar to users.

Put the switcher in a single shared component so both sub-shapes can reuse it.
Locate it wherever shared UI lives in the project.

### 6. Close the feedback loop

Run typecheck and lint to catch issues introduced by variants and switcher.
Typecheck runs project-wide because TypeScript cannot resolve graph from single files.
Lint can target changed files directly.
Only fix errors introduced in changed files.
Ignore unrelated pre-existing errors in repo.
Fix compile and runtime blocker errors before handoff.
Variant that does not compile cannot be previewed in browser.

**Guardrail: Cap fix attempts**
If same error persists after 3 fix attempts, stop and report to user.

### 7. Display Reminder Message

Display a reminder message to the user about the usual workflow:

```text
MAKE SURE TO FOLLOW THE WORKFLOW:
1. Use Agent Skill To Create Many Variants <-- You just did this
2. Use Prompt To Create One More Variant
3. Use Prompt To Extract Specs, And Backup Code
4. Use Prompt And Specs To Create Production Ready Code
5. Delete prototype code

More in the README file of this very agent skill.
```

## Anti-patterns

- **Variants differing only in styling or copy during Macro or Meso runs.**
  Respect chosen calibration tier.
  Macro runs require distinct structural patterns (e.g. tabs vs wizard vs master-detail).
- **Sharing too much code between variants in Macro runs.**
  Shared `<Header>` fine; shared `<Layout>` defeats prototype purpose.
  In Macro runs, each variant must stay free to replace layout container.
- **Wiring variants to real mutations.**
  Read-only prototypes fine.
  If variant mutates data, stub it out.
- **Promoting prototype code directly to production.**
  Prototype code skips tests and error handling.
  Rebuild properly when promoting validated design.
