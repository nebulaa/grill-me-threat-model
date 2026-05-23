---
id: TM-012
title: Unpinned images from public registry
severity: medium
stride: Tampering
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Unpinned images from public registry

## Summary

Manifests use `madhuakula/k8s-goat-*` with `:latest` or implicit latest. Compromised or mutated upstream images change lab behavior without operator awareness.

## Context

| Field | Value |
|-------|-------|
| Asset | Container image integrity |
| Trust boundary | TB-2 |
| Entry point | DF-9 image pull |
| Data flow | DF-9 |
| Component | All `scenarios/*/deployment.yaml`, `metadata-db/values.yaml` |

## Scenario

Attacker compromises Docker Hub account or MITM on insecure registry pull; malicious binary runs privileged in operator environment.

## Attack vectors

- Pull `:latest` on setup
- `imagePullPolicy: Always` in metadata-db chart

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1195.002 | Supply Chain Compromise |
| CWE | CWE-494 | Download of Code Without Integrity Check |

## Response

**Primary response:** mitigate

**Rationale:** Pin digests for operator safety; document build-from-source path for contributors.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| Per-component `infrastructure/` build path documented in READMEs | partial | `infrastructure/*/README` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Add `image@sha256:...` variants in `threat-model/hardened/` |  | none |
| 2 | Document `docker build` flow in operator guide |  | none |
| 3 | **Pedagogy:** `:latest` OK for class setup with trust disclaimer |  | none |
| 4 | cosign verify (future) |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Students learn on moving `:latest` unless pins enforced.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
