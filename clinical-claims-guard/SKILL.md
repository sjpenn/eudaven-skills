---
name: eudaven-clinical-claims-guard
description: Scope limiter for clinical-process claims — keeps process claims attributed to CareValidate (never Eudaven), and locks the sjpenn-approved HIPAA data-boundary sentence verbatim.
---

# eudaven-clinical-claims-guard

Narrow scope limiter for clinical-process content. Called by `eudaven-compliance-gate`
as a sub-check for any `content_type` that touches clinical-process or HIPAA
data-boundary claims — not a replacement for the gate, a component of it. Can
also be run standalone against a single claim or short draft when the full
gate isn't needed.

Source material: Multica issue EUDA-6 — HIPAA claim verification gap, the
resolution narrowing scope to CareValidate-only claims, and sjpenn's exact
approved data-boundary framing. Case: `EUDAVEN-CASE-2026-08-26-PROMOTER-NETWORK`.

## Inputs

- `content_draft` — the text under review
- `claim_list` — explicit clinical/data-handling claims in the draft (extract
  these yourself if the caller didn't supply them — read the draft and list
  every claim about clinical process, diagnosis, treatment, dosing, or who
  touches/stores/accesses patient or medical data)

## Outputs

- `verdict` — `PASS` / `BLOCKED`
- `reframed_claims` — for each BLOCKED claim, a suggested rewrite attributing
  it to CareValidate where the claim can be saved by reattribution; omitted
  for claims that must simply be cut (no rewrite makes them acceptable)
- `boundary_phrase_check` — exact-match result (`match` / `paraphrase` /
  `not_present`) of any HIPAA data-boundary claim against the approved wording

## Workflow

Run both checks. A claim can fail one, the other, or both — report every
failure, don't stop at the first hit within a single claim.

### 1. Attribution check (CareValidate vs. Eudaven)

For each claim in `claim_list`, ask: whose process is this describing?

- Claim is attributable to CareValidate specifically (e.g. "CareValidate's
  clinician network reviews X", "CareValidate's clinical team handles Y") →
  passes.
- Claim is framed as Eudaven's own clinical/data-handling capability, or has
  ambiguous attribution (no clear subject — e.g. "the clinical review process
  confirms X" with no named actor) → BLOCK.
  - `reframed_claims`: rewrite naming CareValidate as the actor, if the
    underlying fact is otherwise accurate and CareValidate is the correct
    actor.
  - If the claim describes something Eudaven's own funnel actually does
    (intake forms, CRM, ad tracking, site analytics) — do not reframe it onto
    CareValidate; that misattributes responsibility the other direction. Cut
    the claim instead and flag it for human review; this skill only narrows
    scope, it does not invent attribution.

### 2. HIPAA / data-boundary phrase check

Scan `claim_list` and `content_draft` for any statement about who touches,
stores, or has access to patient/medical data.

- The only pre-approved framing is the exact sentence: **"CareValidate's
  clinician network handles the clinical visit and medical record."** This
  is a factual statement about CareValidate's scope — it makes no claim about
  Eudaven's own funnel (intake forms, CRM, site analytics, ad pixels).
- No HIPAA/data-boundary claim present → `boundary_phrase_check: not_present`,
  check passes.
- Claim present and matches the approved sentence verbatim →
  `boundary_phrase_check: match`, check passes.
- Claim present but paraphrased or reworded, even if it seems to mean the
  same thing → `boundary_phrase_check: paraphrase`, BLOCK. `reframed_claims`:
  replace with the exact approved sentence verbatim — no substitutions.
- Claim asserts anything about Eudaven's own perimeter (positive or negative
  — including the rejected pattern "Eudaven doesn't touch patient data") →
  BLOCK, regardless of phrase-check result. This is an absolute claim about
  an open, separately-tracked question (Eudaven's own funnel's HIPAA
  posture) and cannot be reframed into a pass — cut it. Do not offer a
  `reframed_claims` rewrite for this one; there is no approved way to make
  an Eudaven-side data-boundary claim ship right now.

## Verdict assembly

- Both checks pass on every claim → `verdict: PASS`, `reframed_claims: []`,
  `boundary_phrase_check: not_present` or `match`.
- Any claim fails either check → `verdict: BLOCKED`, `reframed_claims` lists
  every fixable claim's rewrite (cut-only claims noted as such, not rewritten),
  `boundary_phrase_check` reflects the worst result found (`paraphrase` beats
  `not_present`; an Eudaven-perimeter claim is reported as `paraphrase` if it
  reworded the boundary line, or as its own flagged violation if it didn't
  reference the boundary line at all).

## Precedent cases (EUDA-6)

- Clinical Process Explainer script blocked on an unscoped claim ("the
  clinical review process confirms X") — reframed to name CareValidate as
  the actor.
- HIPAA claim "Eudaven doesn't touch patient data, CareValidate handles
  clinician process" rejected — the first half asserts an unresolved
  Eudaven-side claim; approved replacement keeps only the CareValidate half,
  verbatim.
- Compliance hold cleared once the draft used the exact approved sentence
  with no Eudaven-perimeter assertion attached.

## Dependencies

- `eudaven-persona-bench` (EUDA-9) — for context on which persona is
  attached to the draft (only the Clinical Process Explainer persona is
  expected to carry clinical-process claims at all; a claim from any other
  persona's draft is itself a signal to escalate rather than reframe).
- `eudaven-compliance-gate` (EUDA-10) — the caller. This skill's checks
  correspond to compliance-gate checks (a) and (c); compliance-gate degrades
  to calling this skill directly for any `content_type` carrying clinical or
  data-boundary claims rather than re-implementing the logic inline.
