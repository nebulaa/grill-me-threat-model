---
id: TM-011
title: Insecure private registry scenario
severity: medium
stride: Tampering
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Insecure private registry scenario

## Summary

`poor-registry` exposes a Docker registry on port 5000 (forwarded to 1235) with weak configuration for Scenario 7 — pull/push tampering if reachable.

## Context

| Field | Value |
|-------|-------|
| Asset | Container images in registry |
| Trust boundary | TB-1, TB-5 |
| Entry point | EP-6 |
| Data flow | DF-9 |
| Component | `scenarios/poor-registry/deployment.yaml` |

## Scenario

Learner pushes malicious image to registry, tricks cluster into pulling poisoned image if image references are attacker-controlled.

## Attack vectors

- `http://127.0.0.1:1235/v2/_catalog`
- LAN access if TM-004 unmitigated

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1525 | Implant Internal Image |
| CWE | CWE-306 | Missing Authentication |

## Response

**Primary response:** mitigate

**Rationale:** Pedagogy requires weak registry; operator isolates network.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Tie registry scenario to supply-chain doc in guide |  | none |
| 2 | **Pedagogy:** keep registry vulnerable |  | none |
| 3 | Localhost-only access (depends on TM-004) |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Registry abuse is lesson for Scenario 7.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
