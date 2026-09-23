---
name: ref-input-txt-phone-guardrails
description: Phone number input validation and review guardrails for forms, API fields, contact flows, and any feature where users enter, edit, store, compare, display, or share a phone number.
---

# Input TXT Phone Guardrails

Reference skill for any feature where user can enter, paste, edit, store, compare, display, or share a phone number, WhatsApp number, or similar contact number.

## Guidelines

- Treat phone handling as policy-driven. Do not guess missing product rules.
- Validate on client for UX and on server as source of truth.
- Normalize before storing or comparing. Keep normalization policy consistent across frontend and backend.
- Keep storage policy and display policy separate. One canonical stored value can still have a nicer display format.
- Prefer smallest implementation that matches current codebase patterns. Reuse existing validators, helpers, and test conventions first.
- Treat phone as contact PII. Review where it is exposed, logged, or shared.
- If task would change shared types, shared route constants, auth/security code, or validation contracts with broad impact, stop and ask user first per repo rules.
- If task would change Zod validation schemas, stop and ask user first per repo rules.

## Required Policy Decisions

Before implementation or review, confirm these rules. If codebase already answers them, follow existing policy. If not, ask user concise question instead of inventing defaults.

- Scope: phone only, or phone/WhatsApp/contact numbers generally
- Optional or required
- Clear semantics on update: blank means clear existing value, or leave unchanged
- Canonical storage only, or canonical plus raw user input
- Require leading `+` and country code, or allow local/national numbers
- Digits only after `+`, or allow formatting chars in accepted input
- Minimum and maximum length
- Auto-normalize formatting chars away, or reject them
- Auto-prepend country code, or require explicit full number
- Extension support, or reject extensions
- Duplicate detection based on canonical value or raw value
- Visibility rules: private, booking-only, admin-only, public, or mixed by context
- Search/filter semantics if phone ever becomes searchable

## Default Policy Baseline

When product policy does not override, use this baseline:

- trim surrounding whitespace
- if empty after trim: treat as empty
- if non-empty: require leading `+`
- if non-empty: allow digits only after `+`
- reject spaces, hyphens, parentheses, letters, and other punctuation
- store canonical form matching regex `^\+\d+$`
- compare using canonical value
- do not silently infer missing country code
- do not silently rewrite a local number into an international number

Suggested field error:

- `Enter phone number with country code, e.g. +34123456789`

## Clear Semantics

Do not guess blank-value behavior.

- Decide whether blank means "clear existing value" or "leave unchanged".
- Keep submit payload semantics explicit.
- Frontend omission and backend null-clearing must not conflict.
- Whitespace-only input must follow the same rule as empty-after-trim.
- Test update behavior for both existing-value and empty-value cases.

## Normalization Baseline

When chosen policy allows normalization:

- trim surrounding whitespace
- convert accepted input to one canonical storage format
- if canonical format is `^\+\d+$`, remove no characters unless policy explicitly allows formatted input to be normalized
- keep normalization rules identical across frontend and backend
- use canonical value for equality, dedupe, and persistence

Do not silently rewrite meaning-bearing parts of the number beyond agreed policy.

## Privacy And Misuse Checks

Always evaluate whether entered phone number is later:

- rendered on public profile
- exposed during booking/contact flow
- returned by API to other users
- written to logs, errors, analytics, or telemetry
- exported to third parties
- used to trigger SMS, WhatsApp, or contact actions

Required review points:

- Avoid exposing full phone earlier than product intends.
- Avoid leaking full phone in logs, URLs, error payloads, analytics, and telemetry.
- Validate again at trust boundary closest to dangerous action.
- If phone may become part of auth, OTP, recovery, or identity verification, stop and ask before touching auth/security-sensitive code paths.
- Review abuse paths: scraping, spam contact, enumeration, and bulk harvesting.

## UX Rules

- Give example input or placeholder when format may be unclear.
- Return precise error messages: missing country code, invalid characters, too short, too long, and similar.
- If field is optional, blank should not show error.
- If formatting chars are rejected, reject them consistently at all layers.
- If formatting chars are normalized away, normalize them consistently at blur/submit/server.
- Do not silently invent country code.
- Preserve user intent where useful for editing, but use canonical value for logic.
- Keep clear behavior consistent with backend contract.

## Testing Expectations

Cover normal and edge cases relevant to chosen policy:

- valid international number like `+34123456789`
- missing `+`
- letters in input
- spaces, hyphens, or parentheses when policy rejects them
- blank input
- whitespace-only input
- min/max length
- trim behavior
- canonical comparison behavior
- clear-vs-omit update semantics
- frontend field-level error behavior
- backend schema parity if server validation changed

Include both acceptance-path and failure-path tests when feature risk justifies them.

## Steps

Execute steps sequentially.

### Step 1 - Identify Phone Surface

Find every place phone enters or leaves system for current task: form fields, API payloads, stored models, profile views, booking/contact flows, admin views, logs, analytics, exports, and third-party integrations.

### Step 2 - Confirm Policy

Look for existing project conventions or product rules. If any required policy decision is still ambiguous, ask user concise targeted question before coding.

### Step 3 - Implement Or Review Client Validation

Ensure UX validation exists at correct layer. Check required/optional behavior, exact format rule, trim behavior, and field-level error messages. Reuse existing validation patterns when possible.

### Step 4 - Implement Or Review Server Validation

Ensure server enforcement matches client policy. Check API validators, normalization, persistence semantics, and update semantics. Reuse existing backend validation patterns when possible.

### Step 5 - Implement Or Review Normalization And Storage

Ensure canonicalization rules are explicit, consistent, and applied before storage/comparison. Confirm blank and whitespace-only semantics are intentional and tested.

### Step 6 - Privacy Pass

Review exposure, logging, analytics, sharing, and contact-flow behavior. Escalate to user before touching auth/security-sensitive code paths.

### Step 7 - Test Pass

Add or review focused tests for chosen policy rules, clear semantics, and edge cases. Match existing frontend/backend test patterns and avoid redundant coverage.
