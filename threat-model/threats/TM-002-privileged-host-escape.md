---
id: TM-002
title: Privileged pod escape to operator host
severity: critical
stride: Elevation of privilege
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: intentional
---

# Privileged pod escape to operator host

## Summary

The `system-monitor` workload runs **privileged**, with `hostPID`, `hostIPC`, and `hostPath` mount of `/` at `/host-system`. On local KIND, the node shares the operator's Docker host boundary — successful escape impacts the workstation.

## Context

| Field | Value |
|-------|-------|
| Asset | Host filesystem; operator machine |
| Trust boundary | TB-3 Pod → host node |
| Entry point | EP-1 port-forward :1233 |
| Data flow | DF-6 |
| Component | `scenarios/system-monitor/deployment.yaml` |

## Scenario

Learner completes container-escape scenario: abuses privileged cgroup/namespaces, reads `/host-system/etc/shadow` or mounts, pivots to host Docker socket, compromises other local projects.

## Attack vectors

- Web UI on :1233 → container breakout primitives
- `kubectl exec` into privileged pod
- Secret `goatvault` env for scenario chaining

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1611 | Escape to Host |
| CWE | CWE-250 | Execution with Unnecessary Privileges |

## Response

**Primary response:** mitigate

**Rationale:** **Pedagogy:** keep scenario in isolated VM. **Operator safety:** never run on laptop with sensitive data; use dedicated VM, drop caps in hardened fork for demos only.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Guide callout: Scenario 4 requires privileged — host at risk on local Docker/KIND |  | none |
| 2 | **Hardened profile:** optional `deployment-hardened.yaml` without `hostPath` for read-only demos |  | none |
| 3 | **Pedagogy:** keep current manifest as canonical challenge |  | none |
| 4 | Run lab in disposable VM |  | none |
| 5 | Document host-risk in guide |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Full escape remains the learning objective in standard lab mode.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
