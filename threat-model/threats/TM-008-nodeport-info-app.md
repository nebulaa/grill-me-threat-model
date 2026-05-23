---
id: TM-008
title: NodePort exposes info-app beyond port-forward
severity: medium
stride: Information disclosure
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# NodePort exposes info-app beyond port-forward

## Summary

`internal-proxy-info-app-service` is type `NodePort` on port **30003**, exposing `info-app` on every node. On KIND/Docker desktop, this may be reachable from the host without going through localhost port-forwards.

## Context

| Field | Value |
|-------|-------|
| Asset | Environment information / flags |
| Trust boundary | TB-1 |
| Entry point | EP-2 |
| Data flow | DF-3 |
| Component | `scenarios/internal-proxy/deployment.yaml` |

## Scenario

Learner discovers `localhost:30003` or node IP:30003 while following Scenario 8, gathers cluster env details without completing SSRF chain.

## Attack vectors

- `http://127.0.0.1:30003` from host
- Node IP scan on lab network

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-200 | Exposure of Sensitive Information |

## Response

**Primary response:** mitigate

**Rationale:** Scenario 8 teaches NodePort risk — keep for pedagogy; **hardened profile** uses ClusterIP only.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Optional `deployment-hardened.yaml` with ClusterIP |  | none |
| 2 | **Pedagogy:** retain NodePort in default install |  | none |
| 3 | Hardened manifest: ClusterIP service |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

NodePort exposure is intentional lesson.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
