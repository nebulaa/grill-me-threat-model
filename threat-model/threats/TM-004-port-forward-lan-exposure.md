---
id: TM-004
title: Port-forward binds all interfaces (LAN exposure)
severity: high
stride: Information disclosure
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Port-forward binds all interfaces (LAN exposure)

## Summary

`access-kubernetes-goat.sh` uses `kubectl port-forward ... --address 0.0.0.0` for ports 1230–1236. Any device on the local network can reach intentionally vulnerable HTTP services without authentication.

## Context

| Field | Value |
|-------|-------|
| Asset | All scenario UIs and flags |
| Trust boundary | TB-1 LAN → operator host |
| Entry point | EP-1 |
| Data flow | DF-2, DF-3 |
| Component | `access-kubernetes-goat.sh` |

## Scenario

Operator runs access script on laptop on café Wi‑Fi. Attacker on same LAN browses to `http://<operator-ip>:1232`, abuses SSRF, or harvests training secrets from :1230.

## Attack vectors

- Network scan for ports 1230–1236
- Automated SSRF from another machine to operator's forwarded ports

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-668 | Exposure of Resource to Wrong Sphere |

## Response

**Primary response:** mitigate

**Rationale:** Pedagogy does not require LAN exposure; `127.0.0.1` suffices for local learners.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Change `access-kubernetes-goat.sh` default to `--address 127.0.0.1`; add `--expose-lan` for classroom demos |  | none |
| 2 | Document classroom mode in guide |  | none |
| 3 | Host firewall deny 1230–1236 except loopback (operator) |  | none |
| 4 | **Pedagogy:** unchanged cluster; only access script hardening |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Classroom demos may need LAN; operators must consciously opt in.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
