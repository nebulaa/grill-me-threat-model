---
id: TM-009
title: Over-permissive namespace RBAC (big-monolith)
severity: medium
stride: Elevation of privilege
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Over-permissive namespace RBAC (big-monolith)

## Summary

`big-monolith-sa` Role grants `get/list/watch` on **all** resources in namespace `big-monolith`, enabling secret enumeration beyond intended `webhookapikey` scope — Scenario 16.

## Context

| Field | Value |
|-------|-------|
| Asset | `vaultapikey`, `webhookapikey` secrets |
| Trust boundary | TB-5 |
| Entry point | EP-7 |
| Data flow | DF-7 |
| Component | `scenarios/hunger-check/deployment.yaml` |

## Scenario

Compromised `hunger-check` pod uses SA token to list secrets, reads `vaultapikey`, completes scenario flag.

## Attack vectors

- `kubectl auth can-i` from exec session
- Direct API `GET /api/v1/namespaces/big-monolith/secrets`

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-732 | Incorrect Permission Assignment |
| MITRE ATT&CK | T1552.007 | Container API |

## Response

**Primary response:** mitigate

**Rationale:** Pedagogical misconfiguration; operator ensures namespace is lab-only.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Document least-privilege fix in scenario-16 as secure baseline comparison |  | none |
| 2 | **Pedagogy:** keep broad Role |  | none |
| 3 | Lab-only namespace (operator practice) |  | none |
| 4 | Hardened: least-privilege Role scoped to single Secret |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Bypass is the learning objective.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
