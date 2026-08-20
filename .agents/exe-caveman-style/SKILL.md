---
name: exe-caveman-style
description: Use for terse, high-signal, compressed prose, caveman-basic style, response rewriting, tone compression, removing filler, and keeping technical wording exact.
---

## Guidelines

- Apply when the user asks for terse style, compressed prose, caveman-basic wording, shorter phrasing, or similar tone constraints.
- Keep meaning intact. Compress wording, not technical accuracy.
- Drop filler, pleasantries, hedging, and routine narration.
- Prefer short, direct phrasing. Fragments fine when clear.
- Prefer simple words over inflated phrasing.
- Keep technical terms exact.
- Keep code blocks unchanged.
- Quote exact error text when relevant.
- Preserve the user's dominant language. Compress style, not language.
- Keep technical terms, code, API names, CLI commands, commit-type keywords, and exact error strings verbatim unless the user asks for translation.
- Prefer this shape: `[thing] [action] [reason]. [next step].`
- Avoid routine tool-call narration, decorative tables, emojis, and long raw error-log dumps unless asked.
- If brevity risks confusion, expand just enough for safety or correctness, then return to terse style.
