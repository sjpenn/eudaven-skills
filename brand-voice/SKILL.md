---
name: eudaven-brand-voice
description: Eudaven brand tone/style reference distilled from approved ad creative. Returns voice descriptors, approved lexicon, and per-angle example snippets so copy generation stays consistent without re-briefing each time.
---

# eudaven-brand-voice

Tone/style reference for any agent generating Eudaven-facing copy (social posts,
carousel cards, ad copy, scripts). Source of truth: the 3-angle + 5-card
creative set approved on EUDA-7, itself pulled from `Eudaven_Marketing_Strategy`
§5 (locked messaging) and §8 (creative spec).

## Inputs

- `content_type` — one of: social post, carousel card, ad copy, script
- `angle` (optional) — one of: Journey-led, Personalization-led, Care-led

## Outputs

- Voice descriptors (tone words, sentence rhythm, banned phrases)
- Approved lexicon (terms to use / avoid)
- 1–2 reference example snippets for the requested angle
- `validated: true|false` — whether the requested angle has approved precedent

## Workflow

1. Caller requests a voice profile for a `content_type` (+ optional `angle`).
2. Look up the angle in the Angle Reference Library below.
   - Found → return its descriptors, lexicon notes, and example snippets, `validated: true`.
   - Not found (no `angle` given, or an angle with no approved precedent) → return
     the General Brand Voice baseline below plus a note that it's unvalidated
     for that specific angle, `validated: false`.
3. Before returning any example snippet, cross-check it against the
   **Hard Bans** list. Never surface a snippet, adapted or verbatim, that
   would trip a ban — even if it appeared in an approved deliverable under a
   different context.
4. If asked to generate *new* copy (not just retrieve reference), the caller
   is still responsible for routing the result through `eudaven-compliance-gate`
   before publish — this skill provides tone, not clearance.

## General Brand Voice (baseline — use when no angle is specified or validated)

- **Register:** clinical-warmth. Medically credible without being clinical-cold;
  warm without slipping into lifestyle-influencer hype.
- **Tone words:** calm, personalized, physician-guided, grounded, supportive.
- **Sentence rhythm:** short declarative headline, one supporting clause.
  Avoid stacked adjectives and exclamation points.
- **POV:** speaks to "you" directly (second person) in headlines; third-person
  physician/clinical framing in support copy.
- **Visual pairing (for context, not this skill's output):** sage-green and
  cream palette, warm at-home or outdoor-vitality imagery, telehealth
  consultation moments — never clinical/sterile stock photography.
- **Banned phrasing patterns:** superlatives ("best," "#1," "guaranteed"),
  urgency/scarcity hooks ("limited time," "act now"), before/after language,
  outcome or results claims of any kind ("lose X lbs," "see results in").

## Approved Lexicon

**Use:**
- "personalized," "tailored," "clinically supported," "physician-guided,"
  "ongoing support," "consultation," "care team"
- "GLP-1" as a category term (see hard ban on specific drug names below)

**Avoid:**
- Specific drug/brand names: "Ozempic," "Wegovy," or any other named GLP-1
  product — category term only.
- "Cure," "treatment" (implies clinical claim beyond scope), "diagnosis"
- Any outcome/results/weight-loss-amount claim
- Before/after framing, implied or literal

## Angle Reference Library

### Personalization-led (Angle A)

- **Descriptor:** leads with the individual — copy centers on the person's
  specific plan, not the product category.
- **Example (headline/subheader pair, approved EUDA-7):**
  - Headline: "GLP-1 that's actually tailored to you."
  - Subheader: "Clinical guidance, personalized care."
- **Alt-text register (for accessibility handoff, same tone rules apply):**
  "Warm, calm at-home wellness moment" — not clinical, not staged-dramatic.

### Journey-led (Angle B)

- **Descriptor:** frames GLP-1 care as an ongoing supported process, not a
  single transaction. Emphasizes momentum and continuity over the moment of
  purchase.
- **Example (headline/subheader pair, approved EUDA-7):**
  - Headline: "Your GLP-1 journey, clinically supported."
  - Subheader: "Personalized dosing. Real doctors. Ongoing support."
- **Alt-text register:** "outdoor morning walk conveying vitality and
  momentum" — activity-forward, not outcome-forward (never ties the walk to a
  results claim).

### Care-led (Angle C)

- **Descriptor:** leads with the clinical relationship — physician presence,
  consultation, oversight — rather than the drug or the journey.
- **Example (headline/subheader pair, approved EUDA-7):**
  - Headline: "Personalized GLP-1 care."
  - Subheader: "Physician-guided, from consultation to results." *(see note)*
- **Note:** the word "results" here refers to the consultation-to-maintenance
  process, not a treatment outcome — do not lift this phrase into a context
  where it could read as an outcome claim. When in doubt, use "...from
  consultation to ongoing care" instead.
- **Alt-text register:** "warm telehealth consultation moment between patient
  and physician."

### Carousel structure (5-card, approved EUDA-7)

Use this sequence when `content_type` is "carousel card" and no single angle
is specified — each card has its own register within the shared baseline:

1. **Headline card:** "GLP-1 tailored to you, clinically supported." — sets
   personalization + clinical-support dual promise up front.
2. **Personalization promise:** "Dosing and formulation calibrated to your
   clinical assessment — not a one-size template." — specific, confident,
   no hype.
3. **Clinical support:** "Physician oversight from your first consultation
   through ongoing maintenance." — continuity + credibility.
4. **Trust signals:** LegitScript + licensed-physician-network marks.
   **Hard gate:** never present a LegitScript badge as live/certified unless
   `eudaven-compliance-gate` confirms cert ID issued — placeholder slot only
   until then.
5. **CTA:** "Start your personalized GLP-1 consultation." / "Join the
   waitlist." — low-pressure, consultation-framed, no urgency language.

## Unvalidated Angles / Fallback

If a caller requests an `angle` not listed above (e.g. a new positioning
angle not yet run through creative review), return the **General Brand
Voice** baseline, set `validated: false`, and note explicitly that no
approved precedent exists yet — do not fabricate an example snippet
attributed to an angle that hasn't shipped.

## Compliance Cross-Reference

This skill supplies tone and approved reference copy only — it does not
clear content for publish. Every hard ban listed above mirrors
`eudaven-compliance-gate`'s standing rails (no before/after imagery, no
outcome claims, no named GLP-1 drug products, no live LegitScript badge
pre-cert). If a generation request conflicts with a gate rule, this skill's
lexicon bans take precedence over any single approved example — an approved
snippet is a style reference, not a license to reuse it verbatim in a new
context without a fresh compliance pass.
