# Readme For Humans

This guide is for humans only, to help you dear human being.

# Why Use This Skill?

Phone input breaks easy. Wrong format, leaks, spam. Guard fast:

- policy first, no guessing
- client for UX, server as truth
- one canonical form: `^\+\d+$`, e.g. `+34123456789`
- blank means clear or leave — explicit, never assumed
- phone is PII — check logs, APIs, public views, exports
- stops before auth, shared types, broad validation changes — asks first

## Baseline

- trim, require leading `+`, digits only after `+`
- no silent country-code invent
- error: `Enter phone number with country code, e.g. +34123456789`

# FAQ: Input TXT Phone Guardrails Skill

## When?

Any feature where user enters, edits, stores, compares, displays, or shares phone / WhatsApp number. Form, API, contact flow, profile, booking, admin.

## What?

Reference guardrails: policy decisions, validation, normalization, privacy, UX, tests. 7 steps: surface, policy, client, server, storage, privacy pass, test pass.

## Will it do any code change?

Yes, if asked. Implements or reviews validation per confirmed policy. Asks before broad-impact edits.

## Example Prompt

```markdown
/ref-input-txt-phone-guardrails add phone field to signup form packages/frontend/src/pages/Signup packages/backend/src/routes/auth.ts
```
