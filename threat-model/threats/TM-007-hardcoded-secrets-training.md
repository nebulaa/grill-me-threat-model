---
id: TM-007
title: Hardcoded secrets in code, images, and config
severity: high
stride: Information disclosure
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Hardcoded secrets in code, images, and config

## Summary

Training credentials appear in Dockerfiles (`API_KEY` in users-repo), Redis (`data.redis`), K8s Secrets (base64), and image layers (`hidden-in-layers`). Scenario 1 and 15 depend on this — not production secrets but teach credential hygiene.

## Context

| Field | Value |
|-------|-------|
| Asset | Training flags (`k8s-goat-*`) |
| Trust boundary | TB-5 |
| Entry point | EP-1, EP-5 |
| Data flow | DF-2, DF-8 |
| Component | `infrastructure/users-repo/`, `cache-store/`, `build-code` |

## Scenario

Learner clones repo or pulls image, greps for keys, submits flags. If operator reuses patterns in real apps, bad habits transfer.

## Attack vectors

- `docker history`, `strings` on images
- Git clone of infrastructure/
- Redis `KEYS *` on :6379

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-798 | Use of Hard-coded Credentials |
| MITRE ATT&CK | T1552.001 | Credentials In Files |

## Response

**Primary response:** mitigate

**Rationale:** **Pedagogy:** keep secrets for exercises. **Operator:** ensure flags are not real credentials; add guide disclaimer.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| Hardened profile README states training flags are synthetic | partial | `threat-model/hardened/README.md` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | **Pedagogy:** retain Dockerfile ENV for Scenario 1 |  | none |
| 2 | Pre-commit/secret scanning for contributor accidental real keys |  | none |
| 3 | Scan prevention in CI for real secrets |  | none |
| 4 | Hardened: inject secrets at runtime via Helm for advanced labs only |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Intentional disclosure is the lesson.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
