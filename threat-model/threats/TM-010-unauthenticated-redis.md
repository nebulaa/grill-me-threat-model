---
id: TM-010
title: Unauthenticated Redis cache-store
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

# Unauthenticated Redis cache-store

## Summary

Redis in `secure-middleware` namespace listens on 6379 without AUTH. Data file embeds `SECRETSTUFF` flag. Any pod or port-forward can read keys.

## Context

| Field | Value |
|-------|-------|
| Asset | Redis data / training flag |
| Trust boundary | TB-5 |
| Entry point | EP-5 |
| Data flow | DF-8 |
| Component | `scenarios/cache-store/`, `infrastructure/cache-store/data.redis` |

## Scenario

Learner port-forwards Redis or execs from another pod, runs `KEYS *`, `GET SECRETSTUFF`.

## Attack vectors

- Cluster network to `cache-store-service.secure-middleware:6379`
- Compromised pod lateral movement

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-306 | Missing Authentication for Critical Function |

## Response

**Primary response:** mitigate

**Rationale:** Teaches cache exposure; hardened profile adds Redis AUTH and NetworkPolicy.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Document in scenario docs linking to Redis misconfig |  | none |
| 2 | **Pedagogy:** keep open Redis in default |  | none |
| 3 | NetworkPolicy deny all except lab pods (hardened) |  | none |
| 4 | Redis `requirepass` in hardened profile |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Open Redis is intentional.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
