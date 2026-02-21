# Automate App — Agent Guidelines

## Goal
Build a real native Android automation app (Kotlin + Jetpack Compose) with a rule engine, permissions hub, and logs.
No web UI conversions.

## Non-negotiables
- Implement all triggers/conditions/actions listed in SPEC.md (source of truth).
- Never fake restricted Android capabilities:
  - Airplane mode toggle: restricted → provide Settings fallback + log.
  - Wi-Fi toggle: often restricted → Settings panel fallback + log.
  - VPN: do not invent profiles for third-party VPN apps.

## Workflow
- Work in small PRs (scaffold → models/db → engine → UI → triggers/actions).
- Keep changes buildable at every PR.
- Include a short test plan in PR description.
