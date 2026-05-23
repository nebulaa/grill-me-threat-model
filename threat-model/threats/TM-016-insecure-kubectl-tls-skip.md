---
id: TM-016
title: Optional TLS verification skip on kubectl/helm
severity: low
stride: Tampering
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Optional TLS verification skip on kubectl/helm

## Summary

`setup-kubernetes-goat.sh` and `access-kubernetes-goat.sh` support `--insecure` which sets `kubectl --insecure-skip-tls-verify` and Helm equivalent, enabling MITM against API server during install/access.

## Context

| Field | Value |
|-------|-------|
| Asset | kubectl ↔ API server channel |
| Trust boundary | TB-6 |
| Entry point | EP-7 |
| Data flow | DF-1 |
| Component | setup/access scripts |

## Scenario

Operator uses `--insecure` on untrusted network; attacker MITM injects malicious manifests during apply.

## Attack vectors

- `--insecure` flag on cafe Wi‑Fi
- Corporate TLS inspection without proper trust store

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-295 | Improper Certificate Validation |

## Response

**Primary response:** mitigate

**Rationale:** Flag exists for broken lab certs; document danger; avoid in hardened profile.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Echo warning banner if `--insecure` passed |  | none |
| 2 | **Hardened profile:** disallow flag |  | none |
| 3 | Fix KIND API certs instead of skip |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Low on localhost KIND with loopback API.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
