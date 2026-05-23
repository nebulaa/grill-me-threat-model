---
id: TM-001
title: Lab deployed to wrong kubectl context
severity: critical
stride: Elevation of privilege
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Lab deployed to wrong kubectl context

## Summary

An operator runs `setup-kubernetes-goat.sh` against a non-lab Kubernetes context (shared dev cluster, employer cluster, etc.). The script applies `cluster-admin` bindings and intentionally vulnerable workloads cluster-wide, causing immediate compromise of that environment.

## Context

| Field | Value |
|-------|-------|
| Asset | Cluster-admin equivalent access; all cluster workloads |
| Trust boundary | TB-6 Operator kubectl → cluster |
| Entry point | EP-7 Kubernetes API |
| Data flow | DF-1 |
| Component | `setup-kubernetes-goat.sh`, `scenarios/insecure-rbac/setup.yaml` |

## Scenario

A developer has `kubectl` pointed at a shared staging cluster. They clone Kubernetes Goat and run setup without checking `kubectl config current-context`. The `superadmin` ClusterRoleBinding and privileged deployments land in staging. Any scenario compromise or student exercise now affects shared services.

## Attack vectors

- `kubectl apply` via default kubeconfig context
- CI job reusing production-like credentials
- Copy-paste setup instructions without context verification

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1078 | Valid Accounts |
| CWE | CWE-1268 | Policy Privileges Not Assigned |

## Response

**Primary response:** mitigate

**Rationale:** Unintended deployment is not a teaching goal; preventable with operator guardrails while keeping scenarios intact in an isolated lab context.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Add `setup-kubernetes-goat.sh` guard: print `current-context` and server URL; require `KUBERNETES_GOAT_CONFIRM=yes` or `--i-know-what-im-doing` when context is not `kubernetes-goat-cluster` |  | none |
| 2 | Document dedicated kubeconfig in `guide/docs/how-to-run/kind.md`; never run on shared clusters |  | none |
| 3 | **Hardened profile:** use `KUBECONFIG=~/.kube/goat-config` only; teardown deletes namespace resources |  | none |
| 4 | Dedicated kubeconfig file for lab only |  | none |
| 5 | Context/cluster name prompt in setup script |  | none |
| 6 | Document lab-only cluster in guide prerequisites |  | none |

### Mitigation references (optional)

- OWASP: [Secure CI/CD Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/CI_CD_Security_Cheat_Sheet.html)

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Learners with cluster-admin on a shared cluster can still mis-apply manifests manually; guardrails reduce accidental operator error only.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
