---
id: TM-003
title: Abuse of host container runtime socket
severity: critical
stride: Elevation of privilege
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Abuse of host container runtime socket

## Summary

`health-check` mounts the host container runtime socket (Docker/containerd) via privileged init container and hostPath fallbacks. Compromise grants **root-equivalent control** over all containers on the node — on local KIND, that is the operator's Docker environment.

## Context

| Field | Value |
|-------|-------|
| Asset | Container runtime socket |
| Trust boundary | TB-3 Pod → host |
| Entry point | EP-1 :1231 |
| Data flow | DF-5 |
| Component | `scenarios/health-check/deployment.yaml` |

## Scenario

Learner exploits DIND scenario: from app container reaches socket, runs arbitrary containers on host, extracts images/secrets, stops KIND cluster or mounts host paths.

## Attack vectors

- HTTP app on port 1231
- `kubectl exec` with privileged container
- Socket paths: `/var/run/docker.sock`, `/run/containerd/containerd.sock`

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1610 | Deploy Container |
| CWE | CWE-668 | Exposure of Resource to Wrong Sphere |

## Response

**Primary response:** mitigate

**Rationale:** Intentional for Scenario 2; operator must isolate host. KIND setup comment references socket mount but `kind-cluster-setup.yaml` lacks `extraMounts` — may reduce reliability but socket fallbacks still dangerous when present.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| KIND cluster config without docker.sock `extraMounts` by default | partial | `platforms/kind-setup/kind-cluster-setup.yaml` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Align KIND docs with actual `kind-cluster-setup.yaml` (extraMounts only in documented isolated path) |  | none |
| 2 | **Pedagogy:** retain socket exposure for DIND lesson |  | none |
| 3 | **Operator:** use VM; verify no corporate Docker contexts on same daemon |  | none |
| 4 | Dedicated VM for lab |  | none |
| 5 | Document socket risk in guide Scenario 2 |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Scenario requires socket or simulation substitute; full removal breaks learning outcome.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
