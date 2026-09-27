# Eudaven Skills

Reusable Multica Skills for Eudaven marketing, ads, and persona work.
Each subdirectory is one Skill with its own `SKILL.md`, importable independently via:

```
multica skill import --url github.com/sjpenn/eudaven-skills/tree/main/<dir> --output json
```

## Build tracking

Full specs and build status: Multica issue EUDA-8 (parent) and EUDA-9..EUDA-15 (per-skill).

## Priority / dependency order

1. `persona-bench` and `compliance-gate` — hard dependencies, everything else routes through these
2. `brand-voice`, `ad-creative`, `content-calendar`, `ad-account-ops`, `clinical-claims-guard`
