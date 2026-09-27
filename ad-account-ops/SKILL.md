---
name: eudaven-ad-account-ops
description: Checklist for Eudaven ad account setup dependencies and readiness. Cross-checks eudaven-compliance-gate before returning READY.
---

# eudaven-ad-account-ops

## Purpose

Checklist of the 6 operational/compliance dependencies blocking Meta/TikTok
ad account creation for Eudaven, so the discovery work from EUDA-7 isn't
repeated on the next ad push.

## Inputs

- None required to run the full checklist.
- Optional `dependency_id` (1-6) to check a single item's status instead of
  the full list.

## Outputs

- The 6-item checklist below, each with current status (`open` / `cleared`)
  and owner.
- Overall readiness verdict: `READY` or `BLOCKED` (+ which items are still
  outstanding).

## Workflow

1. Caller requests ad-account readiness (optionally scoped to one
   `dependency_id`).
2. Return the checklist item(s) with status and owner, from the table below.
3. If any item is `open`, verdict = `BLOCKED` — list exactly what's needed to
   clear each open item and who owns clearing it.
4. If all 6 are `cleared`, still run the compliance check in step 5 before
   returning `READY`.
5. **Compliance cross-check** — call `eudaven-compliance-gate` for current
   LegitScript / HIPAA status. Verdict cannot flip to `READY` while any
   compliance-tagged dependency (item 4 below) is unresolved there, even if
   this checklist shows it `cleared` locally — the gate skill is
   authoritative for compliance status, this checklist mirrors it.

## The 6 dependencies

| # | Dependency | Status | Owner | Clears when |
|---|---|---|---|---|
| 1 | Legal business entity info (registered name, address, EIN/tax ID) for Meta Business Manager + TikTok Ads Manager verification | open | sjpenn | Entity details provided and entered into both platforms' verification flows |
| 2 | Payment method on file + explicit spend authorization | open | sjpenn | Payment method added AND explicit go-ahead given (real spend = financial/compliance exposure; not an agent unilateral action) |
| 3 | Domain verification access for properties being advertised | open | sjpenn | DNS/meta-tag access granted for each advertised domain |
| 4 | LegitScript certification confirmed in hand | open | sjpenn (compliance) | Cert received — standing hard gate from EUDA-6; blocks account creation, outreach, filming, AND posting, not just ads, until cleared |
| 5 | Budget sign-off | open | CFO (via sjpenn) | CFO confirms Promoter Network / ad budget numbers |
| 6 | Credential custody model | cleared | Steve | Resolved: Steve holds login credentials per promoter, per channel. Account creation itself is still not a unilateral agent action even after items 1-5 clear — Steve or sjpenn executes account setup once ready |

## Compliance checks

Readiness verdict cannot flip to `READY` while any compliance-tagged
dependency (item 4: LegitScript) is unresolved — this cross-checks
`eudaven-compliance-gate` status rather than trusting a locally-cached
"cleared" value, since LegitScript/HIPAA state can change outside this
checklist's own updates.

## Source material

EUDA-7 discovery: "Ad Account Creation Blocked on 6 Operational/Compliance
Dependencies." Custody model resolution: EUDA-6 decision log (Steve holds
per-promoter, per-channel credentials).
