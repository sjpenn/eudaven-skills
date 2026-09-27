---
name: eudaven-ad-creative
description: Eudaven-specific ad creative generation wrapper — locks in the validated 3-angle framework and 5-card carousel template from EUDA-7 instead of starting from a blank brief each time.
---

# eudaven-ad-creative

Generates DRAFT Eudaven ad copy against the validated EUDA-7 template. Never
skip `eudaven-compliance-gate` — this skill's output is always a gate-pending
draft, never a publish-ready asset.

## Inputs

- `angle` — one of: `Journey-led`, `Personalization-led`, `Care-led`
- `format` — one of: `single post`, `5-card carousel`
- `persona_id` (optional) — if given, pulls voice constraints, compliance
  tier, and claim boundaries from `eudaven-persona-bench` and drafts in that
  persona's voice instead of the brand-account baseline

## Outputs

- Draft ad copy matching the EUDA-7 template structure for the chosen
  angle/format, labeled `DRAFT — PENDING GATE`
- For `5-card carousel`: a card-by-card breakdown (Headline / Personalization
  Promise / Clinical Support / Trust Signals / CTA)
- `gate_status` — always `not yet submitted` on this skill's own output; set
  only after the caller runs the draft through `eudaven-compliance-gate`

## Workflow

1. Caller specifies `angle` + `format` (+ optional `persona_id`).
2. Call `eudaven-brand-voice` for `content_type` = `ad copy` or `carousel
   card` (matching `format`) and the given `angle` to get voice descriptors,
   approved lexicon, and the angle's reference example.
   - `validated: false` comes back (angle has no approved precedent) → do not
     fabricate a template. Fall back to the General Brand Voice baseline and
     flag the draft as `unvalidated angle — no EUDA-7 precedent` instead of
     presenting it as the locked template.
3. If `persona_id` given, call `eudaven-persona-bench` for that persona's
   voice, pillars, compliance tier, and claim boundaries. Draft copy stays
   inside the persona's claim boundaries — never let the angle template's
   phrasing override a persona-level restriction (e.g. lifestyle-cluster
   personas still carry the no-efficacy/no-comparison boundary regardless of
   angle).
   - `eudaven-persona-bench` reports the persona/task as blocked or pending
     for this `task_context` → stop drafting and say so; do not generate a
     draft for a blocked persona.
4. Assemble the draft using the angle's structure (single post) or the
   5-card carousel sequence (see Templates below), substituting persona voice
   where `persona_id` was given.
5. Label the output `DRAFT — PENDING GATE` and hand it to
   `eudaven-compliance-gate` with `content_type: ad_copy`, the `persona_id`
   if any, and an explicit `claim_list` of every factual/data/clinical/
   certification claim in the draft. This skill never marks its own output
   as final — only the gate does, and only with a `PASS` verdict.

## Templates (validated, EUDA-7)

### Single post — by angle

**Personalization-led (Angle A)**
- Headline: "GLP-1 that's actually tailored to you."
- Subheader: "Clinical guidance, personalized care."

**Journey-led (Angle B)**
- Headline: "Your GLP-1 journey, clinically supported."
- Subheader: "Personalized dosing. Real doctors. Ongoing support."

**Care-led (Angle C)**
- Headline: "Personalized GLP-1 care."
- Subheader: "Physician-guided, from consultation to ongoing care."
  (Use "...to ongoing care," not "...to results" — see brand-voice note on
  avoiding an outcome-claim reading of "results" in this context.)

### 5-card carousel (angle-agnostic default)

1. **Headline card:** "GLP-1 tailored to you, clinically supported."
2. **Personalization promise:** "Dosing and formulation calibrated to your
   clinical assessment — not a one-size template."
3. **Clinical support:** "Physician oversight from your first consultation
   through ongoing maintenance."
4. **Trust signals:** LegitScript + licensed-physician-network marks.
   **Hard gate:** never render a LegitScript badge as live/certified in the
   draft unless `eudaven-persona-bench`/compliance status confirms cert
   received — use a placeholder slot until then.
5. **CTA:** "Start your personalized GLP-1 consultation." or "Join the
   waitlist." — consultation-framed, no urgency language.

## Compliance checks

- This skill never bypasses `eudaven-compliance-gate` — every output is
  explicitly labeled `DRAFT — PENDING GATE`, even when reusing a verbatim
  approved template line, because gate clearance is per-asset, not per-phrase.
- Never substitute a named GLP-1 drug/brand product into a template — category
  term ("GLP-1") only, per `eudaven-brand-voice`'s hard bans.
- Never fill the Trust Signals card with a live-looking LegitScript badge
  ahead of confirmed cert receipt.

## Dependencies

- `eudaven-brand-voice` (EUDA-11) — voice descriptors, lexicon, angle
  reference templates.
- `eudaven-persona-bench` (EUDA-9) — persona voice/tier/claim-boundary lookup
  when `persona_id` is given.
- `eudaven-compliance-gate` (EUDA-10) — mandatory pre-publish check for every
  draft this skill produces.

These three are stubs as of this writing (each ships on its own branch under
EUDA-9/10/11). Until they're built and merged, this skill's own workflow
steps above are the spec to follow by hand.

## Source material

EUDA-7 ad creative deliverables: 3 angles (Journey-led, Personalization-led,
Care-led) generated as single Instagram posts, plus a 5-card carousel series
(Headline / Personalization Promise / Clinical Support / Trust Signals /
CTA) — all in review as of that issue.
