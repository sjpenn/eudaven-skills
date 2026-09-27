---
name: eudaven-persona-bench
description: Lookup for the 9 approved Eudaven Promoter Network personas — voice, pillars, cadence, compliance tier, claim boundaries, custody, and status. Use before drafting any script, ad, or calendar slot for a named persona.
---

# eudaven-persona-bench

Single source of truth for the 9 approved Promoter Network personas. Pull persona
detail from here instead of re-deriving it. Source of record: Multica issue EUDA-6
(persona briefs + sign-off thread). Case: `EUDAVEN-CASE-2026-08-26-PROMOTER-NETWORK`.

**Bench-wide status: approved** (sjpenn, EUDA-6). **Outreach-tagged tasks
(creator outreach, account creation, filming, posting) stay blocked until
LegitScript certification is confirmed received** — as of the source thread,
cert is in progress, not yet in hand. Re-verify current cert status before
enabling any outreach-tagged task; this file does not auto-refresh that state.

## How to use this

1. Identify the `persona_id` (name below) and `task_context` (script draft, ad
   creative, calendar slot, outreach).
2. Read that persona's brief: voice, pillars, cadence, compliance flag, claim
   boundaries, status.
3. If `task_context` is outreach-tagged, confirm LegitScript cert status first
   (see above) — don't assume it has landed since this file was written.
4. If the persona's status or compliance flag says blocked/pending for the
   requested task, stop and route through `eudaven-compliance-gate` rather
   than drafting around it.
5. Never return claim language above a persona's compliance flag, and never
   invent claim boundaries not listed below — escalate instead.

## Account custody model (locked)

Steve (sjpenn) owns the login credentials for every promoter, per channel.
No persona gets a separately-owned account without a new sign-off event.

## Compliance flag levels

- **Standard** — routine gate: every post still passes `eudaven-compliance-gate`
  before publish, no elevated review track.
- **Standard, escalation trigger** — standard gate, but a specific claim type
  (e.g. a named peptide/drug) forces sign-off before the draft leaves this stage.
- **Elevated** — highest-risk role. No script leaves draft until Compliance &
  Regulatory Lead clears it. Currently only the Clinical Process Explainer.

## Personas

### Eudaven Official (brand account)
- Voice: calm, clinical-adjacent, evidence-forward. No first-person patient narrative.
- Pillars: mechanism/education, safety, program logistics (how compounding/dispensing works), condition-based education.
- Cadence: 3–4 posts/week. Anchor account for compliance-cleared claims.
- Compliance flag: standard.
- Claim boundaries: only compliance-cleared claims; no persona voice, brand speaks in its own name.
- Status: approved.

### Dana / Priya / Maria (lifestyle cluster)
- Voice: first-person, relatable, journey-oriented — disclosed as promoters/paid talent, never presented as anonymous patients.
- Pillars: daily-life integration, program logistics from a user's-eye view, general wellness.
- Cadence: 2–3 posts/week each, staggered so the feed doesn't read as one voice repeated three times.
- Compliance flag: standard, escalation trigger — any specific peptide/drug name routes through sign-off before the draft leaves this stage.
- Claim boundaries: no efficacy claims, no comparison claims. Disclosure as paid promoter is mandatory.
- Status: approved.

### Marcus / Mike
- Voice: same disclosed-promoter model as the lifestyle cluster, targeted at a male-skewing segment of the funnel.
- Pillars: same as lifestyle cluster; kept visually/tonally distinct so the bench doesn't read as interchangeable.
- Cadence: 2 posts/week.
- Compliance flag: standard, escalation trigger (same peptide/drug-name rule as lifestyle cluster).
- Claim boundaries: no efficacy claims, no comparison claims. Disclosure as paid promoter is mandatory.
- Status: approved.

### Clinical Process Explainer
- Voice: credentialed-sounding but explicitly scoped to *process* (how compounding pharmacies work, how a telehealth visit runs, how dosing titration is medically supervised) — not efficacy, not diagnosis, not comparison.
- Pillars: mechanism/education, safety, cost/access.
- Cadence: 1–2 posts/week — quality over volume given scrutiny level.
- Compliance flag: elevated.
- Claim boundaries: **locked approved script line** — "CareValidate's clinician network handles the clinical visit and medical record." This line is cleared verbatim; do not paraphrase it or reintroduce the rejected broader claim ("Eudaven doesn't touch patient data") — that absolute framing was held because Eudaven's own funnel (intake forms, CRM, ad tracking) has an open, separately-tracked HIPAA-posture question. The approved line sidesteps that question by only asserting what CareValidate handles.
- Status: HIPAA/data-boundary line cleared (Compliance & Regulatory Lead, EUDA-6). Credential/title review track for this persona (on-camera credentials/title language) is a **separate, still-open** item — confirm it has cleared before this persona goes live, independent of the line above.

### Olivia (TikTok-native)
- Voice: fast-cut, trend-literate, Gen Z/younger-millennial register.
- Pillars: myth-busting (safety/mechanism reframed for short-form), day-in-the-life, platform-native trends adapted to program-logistics topics only.
- Cadence: 3–4 short-form posts/week — this account lives on volume and trend velocity.
- Compliance flag: standard — flagged that this cadence stresses review turnaround; plan compliance submission lead time accordingly.
- Claim boundaries: no efficacy claims; trend formats must still land on program-logistics/safety topics, not medical claims.
- Status: approved.

### Renee (empty-nester)
- Voice: warm, unhurried, peer-to-peer for an older demographic.
- Pillars: cost/access, safety, condition-based education framed around midlife health concerns.
- Cadence: 1–2 posts/week.
- Compliance flag: standard.
- Claim boundaries: no efficacy claims, no comparison claims.
- Status: approved.

### Steve (founder, sjpenn's own on-camera persona)
- Voice: founder authority — company vision, why the business exists, behind-the-scenes credibility. Not a testimonial account.
- Pillars: brand story, industry/regulatory commentary, company milestones.
- Cadence: 1 post/week — lower volume, higher weight.
- Compliance flag: standard, extra scrutiny — founder statements carry more implied authority, so word choice on efficacy gets extra scrutiny even at "standard" tier.
- Claim boundaries: no efficacy or comparison claims; brand/vision framing only.
- Status: approved.

## Update rule

Persona data above is sourced from the EUDA-6 sign-off thread. Any change to
voice, pillars, cadence, compliance flag, custody, or claim boundaries requires
a new stakeholder sign-off event on that issue (or a successor) before this
file is edited — do not silently drift the bench from what's approved.
