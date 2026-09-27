---
name: eudaven-compliance-gate
description: Hard compliance choke point for Eudaven content: HIPAA data-boundary framing, LegitScript cert check, CareValidate-scope limiter.
---

# eudaven-compliance-gate

Single choke point every Eudaven marketing/promoter asset passes through before it ships. Run this on any script, ad copy, calendar post, or outreach message before it leaves draft state. Defaults to BLOCKED on ambiguity — never auto-approve an uncertain claim.

## Inputs

- `content_draft` — the text or asset under review
- `content_type` — one of `script`, `ad_copy`, `calendar_post`, `outreach_message`
- `persona_id` — from `eudaven-persona-bench`; carries the persona's compliance tier and claim boundaries
- `claim_list` — explicit factual/data claims made in the content (extract these yourself if the caller didn't supply them — read the draft and list every claim about data handling, clinical process, certification, or outcomes)

## Outputs

- `verdict` — `PASS` / `BLOCKED` / `NEEDS_REVIEW`
- `violations` — list of `{check, reason}` for every failed check
- `required_fix` — specific rewrite instruction, not a vague "fix compliance" note
- `sign_off_required` — boolean; true routes to sjpenn for human clearance

## Workflow

Run checks in this fixed order. Stop at the first hard block — don't keep evaluating once one fails, and don't let a later PASS paper over an earlier BLOCKED.

### a. HIPAA / data-boundary claims

Scan `claim_list` and `content_draft` for any statement about who touches, stores, or has access to patient/medical data.

- The only pre-approved framing is the exact sentence: **"CareValidate's clinician network handles the clinical visit and medical record."** This is a factual statement about CareValidate's scope — it makes no claim about Eudaven's own funnel (intake forms, CRM, site analytics, ad pixels).
- Any paraphrase or rewording of that sentence is a BLOCK, even if it seems to mean the same thing — reject paraphrase drift.
- Any absolute claim about Eudaven's own perimeter is a BLOCK, no exceptions. The rejected pattern this replaces: "Eudaven doesn't touch patient data, CareValidate handles clinician process" — do not let content ship with this or an equivalent absolute claim about Eudaven's boundary.
- No HIPAA/data-boundary claim present → check passes, continue.
- A HIPAA/data-boundary claim present that doesn't match the approved sentence exactly → `BLOCKED`, `required_fix`: replace with the exact approved sentence verbatim.
- The broader question of whether Eudaven's own surfaces ever capture identifiable patient data is open and unresolved — if content asserts anything about that (positive or negative), treat it as an unrecognized claim and escalate per check (e).

### b. LegitScript certification (outreach-tagged content only)

Applies when `content_type == outreach_message` or the persona's channel involves account creation/posting under LegitScript-gated categories.

- Look up the persona's LegitScript cert status via `eudaven-persona-bench`.
- Cert status `received` → passes.
- Cert status `pending` or absent → `BLOCKED`, `required_fix`: hold outreach until cert receipt confirmed; no partial/conditional outreach.

### c. Clinical-process claims scope

Applies to any claim describing a clinical process, diagnosis pathway, or treatment step.

- Scope must stay on CareValidate's process. A clinical-process claim is allowed only when it's attributable to CareValidate specifically (e.g. "CareValidate's clinician network reviews X").
- A clinical-process claim framed as Eudaven's own process, or with ambiguous attribution (no clear subject), is a BLOCK.
- `required_fix`: narrow scope to CareValidate's process, not Eudaven's — name CareValidate as the actor.

### d. Compliance tier check

- Content permissions can't exceed the persona's approved tier (from `eudaven-persona-bench`). A tier-2 persona posting tier-3 content (e.g. direct outcome/efficacy claims) is a BLOCK.
- `required_fix`: cut content to the persona's approved tier, or route to a persona whose tier covers it.

### e. Unrecognized claim type

- Any claim type with no precedent in checks (a)-(d) — including the open Eudaven-side data-boundary question noted in (a) — does not get guessed at.
- Set `sign_off_required: true`, `verdict: NEEDS_REVIEW`. Don't force a PASS or BLOCKED verdict on a claim type this gate has no established rule for.

## Verdict assembly

- Any check (a)-(d) failed → `verdict: BLOCKED`, list every failed check in `violations`, `sign_off_required: false` unless (e) also triggered.
- Check (e) triggered on any claim → `verdict: NEEDS_REVIEW`, `sign_off_required: true`, regardless of whether (a)-(d) passed.
- All checks passed, no unrecognized claims → `verdict: PASS`, `violations: []`, `sign_off_required: false`. Content is released to the publish workflow.

## Precedent cases

- HIPAA data-boundary hold (EUDA-6) — cleared after sjpenn's exact framing above; this is the line every future HIPAA claim is checked against.
- LegitScript outreach gate (EUDA-6) — outreach held until cert receipt, no exceptions for "almost there."
- Clinical process explainer scope narrowing (EUDA-6) — claim rewritten to name CareValidate as the actor instead of an unscoped clinical-process statement.

## Dependencies

- `eudaven-persona-bench` (EUDA-9) — supplies `persona_id` lookups: compliance tier, LegitScript cert status, claim boundaries. Stub as of this writing; this gate's checks (b) and (d) degrade to `sign_off_required: true` if the persona-bench lookup isn't available yet, rather than guessing at tier or cert status.
