---
id: TM-006
title: SSRF via internal-proxy to metadata and cluster
severity: high
stride: Information disclosure
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# SSRF via internal-proxy to metadata and cluster

## Summary

`internal-api` accepts user-controlled URLs and invokes `curl` via `spawnSync` without allowlisting. Attackers reach `metadata-db`, Kubernetes API, and internal Services — Scenario 3 core.

## Context

| Field | Value |
|-------|-------|
| Asset | Simulated credentials; cluster metadata |
| Trust boundary | TB-5 Pod → cluster networks |
| Entry point | EP-3 |
| Data flow | DF-3, DF-4 |
| Component | `infrastructure/internal-api/code/server.js` |

## Scenario

POST to :1232 with `endpoint=http://metadata-db/...` retrieves fake cloud keys; targeting `https://kubernetes.default.svc` with stolen tokens escalates.

## Attack vectors

- `POST /` with attacker-controlled `endpoint`, `method`, `headers`
- Port-forward EP-1 :1232

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CAPEC | CAPEC-664 | Server Side Request Forgery |
| CWE | CWE-918 | SSRF |

## Response

**Primary response:** mitigate

**Rationale:** **Pedagogy:** retain vulnerable proxy. **Operator:** LAN exposure (TM-004) amplifies SSRF from other machines.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Link Scenario 3 docs to SSRF + metadata-db flow in threat model |  | none |
| 2 | **Hardened profile:** optional NetworkPolicy manifest in `threat-model/hardened/` |  | none |
| 3 | **Pedagogy:** no change to `server.js` in default install |  | none |
| 4 | Localhost-only port-forward (depends on TM-004) |  | none |
| 5 | NetworkPolicy deny egress except metadata-db (hardened) |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

SSRF remains exploitable by design in lab mode.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
