---
id: TM-015
title: Guide local dev npm supply chain
severity: low
stride: Tampering
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Guide local dev npm supply chain

## Summary

Contributors running `yarn start` in `guide/` pull Docusaurus and plugins from npm. Compromised dependency or typosquat could affect doc developers — not learners using hosted docs only.

## Context

| Field | Value |
|-------|-------|
| Asset | Developer workstation |
| Trust boundary | TB-7 |
| Entry point | EP-8 |
| Data flow | npm install |
| Component | `guide/package.json`, `package-lock.json` |

## Scenario

Malicious postinstall script in compromised package runs during `npm install` on contributor laptop editing scenario markdown.

## Attack vectors

- `npm install` / `yarn install` without lockfile verify
- Outdated dependencies with known CVEs

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | T1195.002 | Supply Chain Compromise |
| CWE | CWE-829 | Inclusion of Functionality from Untrusted Control Sphere |

## Response

**Primary response:** mitigate

**Rationale:** Standard OSS doc site hygiene; does not affect cluster lab directly.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| `guide/package-lock.json` committed for reproducible installs | partial | `guide/package-lock.json` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |
| Dependabot configuration in repository | partial | `.github/dependabot.yml` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Document `npm ci` in guide/README |  | none |
| 2 | Periodic `npm audit` in CI for guide workspace |  | none |
| 3 | Prefer reading hosted docs vs local dev for learners |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Low for learners; moderate for doc contributors.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
