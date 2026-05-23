---
id: TM-005
title: Pre-provisioned cluster-admin ServiceAccount
severity: high
stride: Elevation of privilege
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Pre-provisioned cluster-admin ServiceAccount

## Summary

`scenarios/insecure-rbac/setup.yaml` creates `superadmin` in `kube-system` bound to `cluster-admin`. Any token theft or scenario leading to SA use yields full cluster control — intentional for advanced scenarios but catastrophic if cluster is not disposable.

## Context

| Field | Value |
|-------|-------|
| Asset | Kubernetes control plane |
| Trust boundary | TB-4 Pod → API |
| Entry point | EP-7 |
| Data flow | DF-7 |
| Component | `scenarios/insecure-rbac/setup.yaml` |

## Scenario

Learner obtains `superadmin` token via secret mount or SSRF to API; creates persistent backdoor, exfiltrates all secrets cluster-wide.

## Attack vectors

- Automount SA token in compromised pod
- `kubectl get secret -n kube-system` if permissions allow

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1078.004 | Cloud Accounts |
| CWE | CWE-269 | Improper Privilege Management |

## Response

**Primary response:** mitigate

**Rationale:** Keep for Scenario 16 pedagogy; **hardened profile** omits or scopes to dedicated namespace with disposable cluster.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| `teardown-kubernetes-goat.sh` removes superadmin ClusterRoleBinding on teardown | partial | `teardown-kubernetes-goat.sh` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | `setup-kubernetes-goat.sh --with-superadmin` (default on for pedagogy, off in hardened doc) |  | none |
| 2 | Teardown verification in guide |  | none |
| 3 | **Pedagogy:** document attack path in scenario-16 content |  | none |
| 4 | Deploy only on disposable KIND cluster (operator practice) |  | none |
| 5 | Hardened: make superadmin install optional flag |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Full cluster takeover is the lesson in standard mode.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
