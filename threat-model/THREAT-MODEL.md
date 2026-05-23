# Threat Model — Kubernetes Goat

## Metadata

| Field | Value |
|-------|-------|
| Version | 1 |
| Architecture baseline | `723a0db478f050d173d23b4ce5044b65bce0bdd0` |
| Last full review | 2026-05-23 |
| In-scope | Deployed lab workloads & RBAC; operator safety on **local** KIND/Docker; `platforms/` bootstrap; `guide/` Docusaurus site |
| Out of scope | Remote cloud cluster targets (EKS/GKE/AKS production); optional bench/Kyverno unless manually applied; external Netlify-hosted docs runtime |

## Risk profile

| Factor | Assessment |
|--------|------------|
| Exposure | Local port-forwards **0.0.0.0:1230–1236**; home **1234**; NodePort **30003**; guide dev **localhost:3000** |
| Sensitive data | Training flags; `kubeconfig`; host FS & container sockets via privileged pods |
| Compliance | None — educational lab |
| Overall sensitivity | **High** on operator workstation (host escape paths); **medium** within isolated VM dedicated to lab |

## System assumptions

- Goals: **(A)** catalog intentional training attack surface; **(B)** operator/learner safety when running locally.
- Environment: **local** KIND (or equivalent) on operator machine — not co-located with production workloads.
- Mitigations: document **both** pedagogical preservation and a **hardened operator profile** (separate context, bind addresses, isolation).
- Vulnerabilities in scenario manifests are **intentional** unless tagged as accidental operator risk.
- Images from Docker Hub `madhuakula/k8s-goat-*`; Helm deploys `metadata-db`.
- Operator has cluster-admin to install; scenarios escalate from weaker footholds.

**Provisional:** none (scope confirmed 2026-05-23).

## Session

| Field | Value |
|-------|-------|
| Status | `in-progress` |
| Last reviewed | `none` |
| Next threat | [TM-001](./threats/TM-001-wrong-cluster-deployment.md) |
| Paused at | — |

### Open questions

- None blocking enumeration.

## Threat index

Sorted by severity (critical first). Link every file in `threats/`.

**Response** = team decision (`avoid` / `mitigate` / `accept` / `transfer`). **Implementation** = `mitigation-state` from verified **Existing controls** only — not from **Recommended actions** or planned `response`.

| ID | Title | Severity | STRIDE | Response | Implementation |
|----|-------|----------|--------|----------|----------------|
| [TM-001](./threats/TM-001-wrong-cluster-deployment.md) | Lab deployed to wrong kubectl context | Critical | Elevation of privilege | Mitigate | non-mitigated |
| [TM-002](./threats/TM-002-privileged-host-escape.md) | Privileged pod escape to operator host | Critical | Elevation of privilege | Mitigate | non-mitigated |
| [TM-003](./threats/TM-003-container-runtime-socket.md) | Abuse of host container runtime socket | Critical | Elevation of privilege | Mitigate | partially-mitigated |
| [TM-004](./threats/TM-004-port-forward-lan-exposure.md) | Port-forward binds all interfaces (LAN exposure) | High | Information disclosure | Mitigate | non-mitigated |
| [TM-005](./threats/TM-005-cluster-admin-superadmin-sa.md) | Pre-provisioned cluster-admin ServiceAccount | High | Elevation of privilege | Mitigate | partially-mitigated |
| [TM-006](./threats/TM-006-ssrf-internal-proxy.md) | SSRF via internal-proxy to metadata and cluster | High | Information disclosure | Mitigate | non-mitigated |
| [TM-007](./threats/TM-007-hardcoded-secrets-training.md) | Hardcoded secrets in code, images, and config | High | Information disclosure | Mitigate | partially-mitigated |
| [TM-008](./threats/TM-008-nodeport-info-app.md) | NodePort exposes info-app beyond port-forward | Medium | Information disclosure | Mitigate | non-mitigated |
| [TM-009](./threats/TM-009-namespace-rbac-bypass.md) | Over-permissive namespace RBAC (big-monolith) | Medium | Elevation of privilege | Mitigate | non-mitigated |
| [TM-010](./threats/TM-010-unauthenticated-redis.md) | Unauthenticated Redis cache-store | Medium | Information disclosure | Mitigate | non-mitigated |
| [TM-011](./threats/TM-011-exposed-poor-registry.md) | Insecure private registry scenario | Medium | Tampering | Mitigate | non-mitigated |
| [TM-012](./threats/TM-012-unpinned-image-supply-chain.md) | Unpinned images from public registry | Medium | Tampering | Mitigate | partially-mitigated |
| [TM-013](./threats/TM-013-metadata-db-credential-simulation.md) | Simulated cloud credentials via metadata-db | Medium | Information disclosure | Mitigate | non-mitigated |
| [TM-014](./threats/TM-014-aws-cdk-remote-lab-footgun.md) | AWS CDK EC2 path expands blast radius | Medium | Information disclosure | Mitigate | partially-mitigated |
| [TM-015](./threats/TM-015-guide-npm-supply-chain.md) | Guide local dev npm supply chain | Low | Tampering | Mitigate | partially-mitigated |
| [TM-016](./threats/TM-016-insecure-kubectl-tls-skip.md) | Optional TLS verification skip on kubectl/helm | Low | Tampering | Mitigate | non-mitigated |
| [TM-017](./threats/TM-017-no-install-context-guard.md) | Setup scripts lack cluster context guard | Informational | Tampering | Mitigate | non-mitigated |

## Summary (update after walkthrough or version bump)

Counts by **implementation** (`mitigation-state`), not by planned `response`.

| Severity | Total | Non-mitigated | Partial | Full | Response open |
|----------|-------|---------------|---------|------|---------------|
| Critical | 3 | 2 | 1 | 0 | 3 |
| High | 4 | 2 | 2 | 0 | 4 |
| Medium | 6 | 4 | 2 | 0 | 6 |
| Low | 2 | 1 | 1 | 0 | 2 |
| Informational | 1 | 1 | 0 | 0 | 1 |

**Response open** — `response` is `mitigate` or `avoid` but `mitigation-state` is not `fully-mitigated`.

### Residual risk (stakeholder view)

Enumeration complete; walkthrough not started. Top operator risks (by impact, **implementation** state): wrong cluster (TM-001, non-mitigated), host compromise via privileged/socket paths (TM-002 non-mitigated; TM-003 partially-mitigated), LAN exposure via port-forward (TM-004, non-mitigated). Partial controls (e.g. teardown, KIND defaults, hardened README) do not close these scenarios.

## Version history

| Version | Date | Reason |
|---------|------|--------|
| 1 | 2026-05-23 | Initial model — scope confirmed, system model + enumeration |
| 1 | 2026-05-23 | Threat entries aligned to skill format: Existing controls vs Recommended actions; index **Implementation** column |
