---
id: TM-013
title: Simulated cloud credentials via metadata-db
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

# Simulated cloud credentials via metadata-db

## Summary

Helm chart `metadata-db` serves static files mimicking cloud instance metadata including `secrets/kubernetes-goat`. Combined with SSRF (TM-006), learners practice credential theft from metadata service pattern.

## Context

| Field | Value |
|-------|-------|
| Asset | Simulated API keys in metadata paths |
| Trust boundary | TB-5 |
| Entry point | EP-4 |
| Data flow | DF-4 |
| Component | `infrastructure/metadata-db/metadata/` |

## Scenario

SSRF to `http://metadata-db/latest/secrets/kubernetes-goat` returns training credentials; on real cloud, same pattern exfiltrates IAM keys.

## Attack vectors

- SSRF from internal-proxy
- Direct cluster DNS from compromised pod

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1552.005 | Cloud Instance Metadata API |
| CAPEC | CAPEC-664 | SSRF |

## Response

**Primary response:** mitigate

**Rationale:** Core Scenario 3/12 pedagogy; operator must not deploy metadata-db pattern in real workloads.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Cross-link scenario-3 and scenario-12 to metadata-db threat |  | none |
| 2 | **Pedagogy:** keep static metadata files |  | none |
| 3 | Guide: IMDSv2 / metadata hop limit on real clouds |  | none |
| 4 | NetworkPolicy restrict metadata-db to internal-proxy NS (hardened) |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Simulation intentionally teaches dangerous pattern.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
