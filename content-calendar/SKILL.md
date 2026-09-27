---
name: eudaven-content-calendar
description: Generates a week-by-week content calendar with persona rotation logic for the Eudaven Promoter Network. Use when scheduling posts across the approved persona bench for any date range.
---

# eudaven-content-calendar

Templates the 4-week cadence + persona rotation logic built for the Oct 10–Nov 6
2026 launch calendar so later cycles reuse the structure instead of rebuilding it.
Source of record: Multica issue EUDA-6 (persona bench) and EUDA-13 (this skill).
Case: `EUDAVEN-CASE-2026-08-26-PROMOTER-NETWORK`.

Every calendar this skill produces is **DRAFT** until every flagged slot clears
`eudaven-compliance-gate`. Never hand a generated calendar to production as final.

## Inputs

- `start_date` — first day of the calendar (any weekday; weeks run start_date → start_date+6).
- `weeks` — number of weeks to generate. Default `4`.
- `persona_ids` — personas to include. Default: all 9 approved personas in
  `eudaven-persona-bench` (Eudaven Official, Dana, Priya, Maria, Marcus, Mike,
  Clinical Process Explainer, Olivia, Renee, Steve — bench-wide status must
  read approved before defaulting to "all").

## Workflow

1. **Read the bench.** For each `persona_id`, pull cadence, pillars, compliance
   flag, and status from `eudaven-persona-bench`. Don't re-derive or guess this
   data — the bench is the single source of truth.
2. **Convert cadence to a weekly slot count.** Cadence is given as a range
   (e.g. "2–3 posts/week"); alternate between the low and high end week over
   week so the average across the full calendar matches the approved cadence,
   rather than always rounding the same direction.
3. **Place slots on the grid.** One row per persona per slot, spread across the
   week so no two personas from the same voice cluster (lifestyle cluster:
   Dana/Priya/Maria/Marcus/Mike) land on the same day, and so a single persona
   never posts twice in one day. Assign each slot a content pillar drawn from
   that persona's approved pillar list (rotate pillars so one doesn't dominate
   a persona's week).
4. **Apply compliance flags per slot:**
   - `elevated` (Clinical Process Explainer) → every slot is marked
     **COMPLIANCE HOLD** — route through `eudaven-compliance-gate` before the
     draft leaves this stage, regardless of topic.
   - `standard, escalation trigger` (lifestyle cluster, Marcus/Mike, Olivia) →
     mark **COMPLIANCE HOLD** only on slots whose pillar/topic names a specific
     peptide or drug; otherwise mark **standard gate** (still reviewed, no
     elevated track).
   - `standard` / `standard, extra scrutiny` → mark **standard gate**.
5. **Apply persona status.** If a persona's bench status is not `approved` for
   the task context (e.g. outreach-tagged work still LegitScript-blocked, or a
   credential/title review still open), do not place it into a live slot —
   mark every one of its slots **TENTATIVE** and note the blocking condition.
   Re-check status at generation time; it can change between calendar runs.
6. **Output the grid** as a week-by-week table (see format below), one table
   per week, followed by a rotation-balance check.
7. **Rotation-balance check.** After placing all slots, verify each persona's
   total slot count across the full calendar matches its approved cadence ×
   weeks (within the low/high range) and that no pillar/persona combination
   listed as blocked/pending appears in a non-TENTATIVE row. Flag any mismatch
   before returning the calendar.

## Output format

One table per week:

| Date | Persona | Pillar | Format | Gate | Status |
|------|---------|--------|--------|------|--------|
| 2026-10-10 | Eudaven Official | mechanism/education | Feed post | standard gate | scheduled |
| 2026-10-11 | Dana | daily-life integration | Reel | standard gate | scheduled |
| 2026-10-11 | Clinical Process Explainer | safety | Feed post | COMPLIANCE HOLD | scheduled |
| 2026-10-13 | Olivia | myth-busting | Short-form | standard gate | scheduled |

`Status` is `scheduled` for approved personas with cleared gates, or
`TENTATIVE — <reason>` for anything blocked (e.g. `TENTATIVE — LegitScript cert
pending`). Never mark a blocked persona `scheduled`.

Close the output with: **"DRAFT — N slot(s) require eudaven-compliance-gate
clearance before this calendar is final."** (N = count of COMPLIANCE HOLD +
TENTATIVE rows; if zero, state the calendar has no outstanding gates but is
still draft until a stakeholder confirms it).

## Update rule

Rotation logic and compliance-flag mapping above are derived from the approved
persona bench (EUDA-6) and the Oct 10–Nov 6 2026 launch calendar. If the bench
changes (new persona, cadence change, compliance flag change), re-pull from
`eudaven-persona-bench` — do not let this skill's examples drift from the
bench's current state.
