---
name: grill-threat-model
description: >-
  Structured threat modeling workflow grounded in repo architecture docs and code.
  Produces and maintains threat-model/ artifacts (system model, STRIDE threats,
  mitigations, session state). Use when the user wants threat modeling, security
  review of architecture, STRIDE/DREAD analysis, or to resume a threat-model session.
disable-model-invocation: true
---

# Grill Threat Model

Interview the user through threats **one at a time**, highest severity first. Ground every claim in architecture docs and code. Update `threat-model/` **inline** as decisions crystallize — do not batch documentation until the end.

If a question can be answered by exploring the codebase or existing docs, explore instead of asking.

## Repo layout

Create `threat-model/` lazily when the first artifact is needed:

```
/
├── threat-model/
│   ├── THREAT-MODEL.md       # Index: scope, assumptions, risk profile, session, version
│   ├── system-model.md       # DFD, trust boundaries, assets, flows, interactors
│   └── threats/
│       ├── TM-001-slug.md
│       └── ...
├── CONTEXT.md                # Optional — domain language from grill-with-docs
├── docs/                     # Architecture, ADRs, README
└── src/
```

Formats: [THREAT-MODEL-FORMAT.md](./THREAT-MODEL-FORMAT.md), [THREAT-ENTRY-FORMAT.md](./THREAT-ENTRY-FORMAT.md). Framework reference: [FRAMEWORKS-REFERENCE.md](./FRAMEWORKS-REFERENCE.md).

## Phase 0 — Discover

1. Read architecture docs: `README*`, `docs/`, `CONTEXT.md`, `CONTEXT-MAP.md`, `docs/adr/`, diagrams, infra (Terraform, K8s, Docker), OpenAPI/GraphQL specs.
2. Map high-level components by reading entry points: apps, APIs, auth, data stores, queues, third-party integrations.
3. Note contradictions between docs and code; surface them before modeling threats.

## Phase 1 — Confirm scope (one question block, then wait)

Present findings and ask the user to confirm or correct:

| Item | Capture in `THREAT-MODEL.md` |
|------|------------------------------|
| **Scope** | In-scope components, environments, and explicit out-of-scope |
| **System assumptions** | Trust levels, deployment model, auth model, data residency |
| **Risk profile** | Internet-facing?, PII/sensitive data?, compliance (SOC2, HIPAA, PCI), blast radius |

Do not generate the full threat list until scope is confirmed. If the user defers, document assumptions as **provisional** and label threats that depend on them.

## Phase 2 — System model

Write or update `threat-model/system-model.md` using [THREAT-MODEL-FORMAT.md](./THREAT-MODEL-FORMAT.md#system-model):

- **Scope** — what is protected
- **Assets** — sensitive data, systems, credentials, keys
- **Interactors** — external entities (users, admins, partners, other systems)
- **Components** — frontend, APIs, auth, workers, data stores
- **Trust boundaries** — network segments, RBAC zones, tenant isolation, cloud accounts
- **Entry / exit points** — how attackers get in; how data leaves
- **Data flows** — direction, protocol, data type, authn/authz
- **DFD** — Mermaid `flowchart` or `C4Context` diagram

Cross-reference code when the user states how something works. Call out mismatches immediately.

## Phase 3 — Enumerate threats

Generate threats from the system model using **STRIDE** per component/boundary. Enrich with:

- **Attack vectors** — concrete channels (API, SSRF, supply chain, physical, social)
- **External IDs** when applicable: MITRE ATT&CK technique, CAPEC, CWE (see [FRAMEWORKS-REFERENCE.md](./FRAMEWORKS-REFERENCE.md))

Assign **severity** (use one scale consistently):

| Severity | Guidance |
|----------|----------|
| **Critical** | Trivial exploit + high impact on confidentiality/integrity/availability or compliance |
| **High** | Realistic exploit with significant impact |
| **Medium** | Requires conditions or limited impact |
| **Low** | Difficult exploit or minimal impact |
| **Informational** | Hardening, defense-in-depth, or monitoring gaps |

For each threat, draft optional **DREAD** or **CVSS 4.0** notes when ranking is ambiguous (calculator: https://www.first.org/cvss/calculator/4.0#).

Create one file per threat under `threat-model/threats/` using [THREAT-ENTRY-FORMAT.md](./THREAT-ENTRY-FORMAT.md). Update `THREAT-MODEL.md` threat index sorted by severity.

**Adam Shostack checkpoint** (internal, not necessarily written to disk until review):

1. What are we working on? → `system-model.md`
2. What can go wrong? → `threats/`
3. What are we going to do about it? → user walkthrough + mitigations
4. Did we do a good job? → end-of-session summary in `THREAT-MODEL.md`

## Phase 4 — Walkthrough with user

Process threats **one at a time**, **critical → informational**. For each threat:

1. Summarize: asset, boundary, STRIDE category, scenario, severity rationale.
2. State your **recommended response** (avoid / mitigate / accept / transfer) with **concrete, actionable** steps tuned to project maturity and risk profile. Be opinionated; explain trade-offs briefly.
3. Ask the user to choose or adjust the response.
4. Update the threat file immediately: `status`, `response`, `mitigations`, `owner`, `notes`.

**Response types** (document exactly one primary response per threat):

| Response | Meaning |
|----------|---------|
| **Avoid** | Eliminate the exposure (design change, remove feature, de-scope) |
| **Mitigate** | Reduce likelihood or impact with controls |
| **Accept** | Acknowledged residual risk; document justification |
| **Transfer** | Insurance, vendor SLA, shared responsibility with third party |

Link mitigations to [MITRE D3FEND](https://d3fend.mitre.org/), [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html), or Microsoft Threat Modeling categories when helpful — keep recommendations implementable, not hypothetical.

Classify mitigation state:

- **Non-mitigated** — no effective control; exploitable
- **Partially mitigated** — limits impact or difficulty
- **Fully mitigated** — control matches threat; residual risk accepted only if documented

If the user pauses, update `THREAT-MODEL.md` **Session** section (see format) with `last-reviewed`, `next-threat-id`, and open questions. On resume, read Session first and continue from `next-threat-id`.

## Phase 5 — Versioning and refresh

Track in `THREAT-MODEL.md`:

```yaml
version: 1
architecture-baseline: <git sha or tag or "undocumented">
last-full-review: YYYY-MM-DD
```

Start a **new version** when architecture changes would invalidate **>25%** of threats (major component split, new trust boundary, auth redesign, new data store class). Then:

1. Increment `version`
2. Add `threat-model/archive/v{N-1}/` or mark superseded threats `status: superseded`
3. Re-run Phase 0–2; diff system model; re-enumerate or re-validate each threat

## During the session

### Ground in architecture docs

Prefer terms from `CONTEXT.md` / ADRs. If the user describes a component that does not exist in docs or code, ask whether it is planned or mistaken.

### Challenge vague claims

Replace "secure the API" with specific controls (authn scheme, rate limits, input validation, mTLS). Replace "encrypt data" with where (at rest, in transit), what keys, who holds them.

### Discuss concrete scenarios

For each high-severity threat, walk through a plausible attack story: entry point → pivot → asset impact. Use scenarios to validate trust boundaries.

### Update files inline

When scope, a flow, or a threat response is resolved, write it immediately. Do not wait for session end.

### One question at a time in walkthrough

During Phase 4, present one threat, get feedback, update the file, then proceed.

## End of session

Update `THREAT-MODEL.md`:

- **Summary** — counts by severity and mitigation state (non / partial / full)
- **Residual risk** — top unmitigated or accepted items for stakeholders
- **Session** — clear `next-threat-id` or mark `complete`
- **Open questions** — architecture gaps blocking analysis

## Multi-context repos

If `CONTEXT-MAP.md` exists, scope may be one context. Document which contexts are in scope in `THREAT-MODEL.md`. Prefer a single `threat-model/` at repo root; for large monorepos, optional `src/{context}/threat-model/` with a root index linking them.

## What not to do

- Do not store secrets, credentials, or live exploit payloads in threat-model files.
- Do not mark threats **fully mitigated** without pointing to an existing control in code or config (or a tracked ticket the user confirms).
- Do not skip severity ordering during walkthrough unless the user explicitly reprioritizes.
