# System Model

## Scope

Aligned with [THREAT-MODEL.md](./THREAT-MODEL.md) (confirmed 2026-05-23).

**In scope**

- **Deployed lab** (`scenarios/`, `infrastructure/` images): intentional vulnerabilities as **training attack surface** (goal A) and **operator impact** when run locally (goal B).
- **Access path**: `access-kubernetes-goat.sh` port-forwards, in-cluster Services/NodePorts.
- **Local platform**: `platforms/kind-setup/` (primary path), including KIND cluster on operator workstation/Docker.
- **Platforms reference**: `platforms/aws-setup/` CDK stacks (documented alternate; relevant to guide content even when primary env is local).
- **Guide**: `guide/` Docusaurus site (`yarn start` / static build) as documentation delivery and local dev surface.

**Out of scope**

- Managed cloud control planes (EKS/GKE/AKS) as production targets — **local KIND/minikube-style only** for deployment assumptions.
- Optional scenarios not applied by `setup-kubernetes-goat.sh` (kube-bench, docker-bench, Kyverno) unless manually enabled.
- Attacks against madhuakula.com hosted docs (external Netlify) beyond what the repo ships.

**Dual mitigation stance**

- **Pedagogy**: preserve scenario weaknesses; threats marked `training-surface: intentional`.
- **Operator safety / hardened profile**: document controls (dedicated cluster, context guards, bind addresses, network policy) without removing lab value.

## Assets

| Asset | Type | Sensitivity | Location / store |
|-------|------|-------------|------------------|
| Cluster-admin equivalent access | Capability | Critical | `superadmin` SA + operator `kubeconfig` |
| Host filesystem (operator machine) | System | Critical | Mounted via `hostPath` / privileged pods |
| Container runtime socket | System | Critical | Host Docker/containerd via `health-check` |
| Training flags / API keys | Data | Medium (lab) | Secrets, Redis, Dockerfiles, metadata files |
| Learner `kubeconfig` | Credential | High | Operator `~/.kube/config` |
| KIND node / Docker graph | Infrastructure | High | Local Docker |
| Guide npm dependencies | Supply chain | Low–Medium | `guide/package-lock.json` |
| CDK-deployed EC2 (optional) | Infrastructure | High | AWS (if operator follows AWS guide) |

## Interactors

| Interactor | Type | Trust level | Notes |
|------------|------|-------------|-------|
| Learner / student | Person | Semi-trusted | Runs exploits against lab; may be novice |
| Lab operator | Person | Privileged | Installs with cluster-admin; owns host |
| LAN peer | Person/System | Untrusted | Can reach `0.0.0.0` port-forwards |
| Container workloads | Process | Untrusted | Intentionally vulnerable apps |
| Docker Hub registry | System | Semi-trusted | `madhuakula/k8s-goat-*` images |
| Kubernetes API | System | Trusted boundary | Becomes hostile if wrong context |

## Components

| Component | Role | Technology |
|-----------|------|------------|
| `setup-kubernetes-goat.sh` | Deploy RBAC + manifests + Helm | Bash, kubectl, helm |
| `access-kubernetes-goat.sh` | Expose workloads locally | kubectl port-forward |
| `build-code` | Secrets-in-code scenario | Container :3000 |
| `health-check` | DIND / socket scenario | Privileged pod, busybox init |
| `internal-proxy` | SSRF + info disclosure | Node.js `curl` proxy + info-app |
| `system-monitor` | Container escape scenario | Privileged, host mounts |
| `metadata-db` | Cloud metadata SSRF target | Helm, Go static file server |
| `kubernetes-goat-home` | Scenario index UI | Static web |
| `poor-registry` | Registry attack scenario | Registry :5000 |
| `cache-store` | Redis secret scenario | Redis in `secure-middleware` |
| `hunger-check` | DoS + RBAC bypass | `big-monolith` namespace |
| `hidden-in-layers` / `batch-check` | Image/job scenarios | Job |
| `platforms/kind-setup` | Local cluster bootstrap | KIND, Docker |
| `platforms/aws-setup/*-cdk` | Remote lab VM bootstrap | AWS CDK, EC2 |
| `guide/` | Documentation | Docusaurus 3, React |

## Trust boundaries

| Boundary | Separates | Controls (current) |
|----------|-----------|-------------------|
| TB-1 Internet/LAN → operator host | Remote users from lab ports | None by default; PF binds `0.0.0.0` |
| TB-2 Host → KIND cluster | Docker host from cluster | Docker socket (optional mount per docs) |
| TB-3 Pod → host node | Workloads from host OS | Weak: privileged, hostPath, hostPID |
| TB-4 Pod → Kubernetes API | Workloads from control plane | RBAC (misconfigured by design) |
| TB-5 Pod → cluster networks | Namespace isolation | Partial; `default`, `big-monolith`, `secure-middleware` |
| TB-6 Operator kubectl → cluster | Human from API | Relies on correct context only |
| TB-7 Guide dev → browser | Docs vs user | Localhost Docusaurus defaults |

## Entry points

| ID | Entry | Protocol / interface | Auth |
|----|-------|----------------------|------|
| EP-1 | Port-forward 1230–1236, 1234 | HTTP/TCP localhost/LAN | None |
| EP-2 | NodePort 30003 (`info-app`) | HTTP | None |
| EP-3 | `internal-proxy` POST `/` | HTTP SSRF proxy | None |
| EP-4 | `metadata-db` ClusterIP/HTTP | HTTP file server | None |
| EP-5 | Redis `cache-store:6379` | Redis | None |
| EP-6 | `poor-registry` :5000 | Docker registry API | Weak/default |
| EP-7 | Kubernetes API | HTTPS | SA tokens / operator creds |
| EP-8 | `guide` `yarn start` | HTTP :3000 | None (dev) |
| EP-9 | CDK EC2 :1230–1234 (optional) | HTTP if AWS path used | SG whitelist IP |

## Exit points

| ID | Exit | Data leaving | Controls |
|----|------|--------------|----------|
| XP-1 | SSRF from internal-api | HTTP to metadata, internal svcs | None |
| XP-2 | Exfil via compromised pod | Secrets, kube API objects | None by design |
| XP-3 | hostPath read/write | Host files | None |
| XP-4 | Image pull | Workload compromise | Docker Hub TLS only |
| XP-5 | Learner notes / screenshots | Flags | N/A |

## Data flows

| ID | From | To | Data | Direction | Protocol | Authn / Authz |
|----|------|-----|------|-----------|----------|---------------|
| DF-1 | Operator | Kubernetes API | Manifests, RBAC | → | HTTPS | kubeconfig |
| DF-2 | Learner browser | build-code :1230 | Source/secrets | → | HTTP | None |
| DF-3 | Learner | internal-proxy :1232 | SSRF requests | → | HTTP POST | None |
| DF-4 | internal-proxy | metadata-db / cluster svcs | Tokens, metadata | → | HTTP | None |
| DF-5 | health-check pod | host container socket | Container API | ↔ | Unix socket | None |
| DF-6 | system-monitor pod | `/host-system` | Host FS | ← | mount | Privileged |
| DF-7 | hunger-check SA | K8s API secrets | vault/webhook keys | → | HTTPS | RBAC (overbroad Role) |
| DF-8 | Learner | Redis :6379 | `SECRETSTUFF` | → | Redis | None |
| DF-9 | kubectl | Docker Hub | Container images | ← | HTTPS | Image pull |
| DF-10 | Developer | guide dev server | Static docs | → | HTTP | None |

## Data flow diagram

```mermaid
flowchart TB
  subgraph host["Operator host (TB-1, TB-2)"]
    Learner([Learner browser])
    PF[access-kubernetes-goat.sh\nport-forward 0.0.0.0]
    Docker[(Docker / KIND)]
  end

  subgraph cluster["KIND cluster (TB-3–TB-5)"]
    Home[kubernetes-goat-home :1234]
    Build[build-code :1230]
    Health[health-check :1231\nprivileged + socket]
    Proxy[internal-proxy :1232\nSSRF]
    Monitor[system-monitor :1233\nhostPath /]
    Meta[(metadata-db)]
    Redis[(cache-store Redis)]
    Reg[poor-registry :1235]
    API[(Kubernetes API)]
    SA[superadmin SA\ncluster-admin]
  end

  Guide[guide Docusaurus\nyarn start :3000]

  Learner --> PF
  PF --> Home & Build & Health & Proxy & Monitor & Reg
  Proxy -->|DF-4 SSRF| Meta
  Proxy --> API
  Health -->|DF-5| Docker
  Monitor -->|DF-6 host mount| Docker
  Build -.->|secrets in image| Learner
  Redis -->|DF-8 no auth| Learner
  SA --> API
  Operator([Operator kubectl]]) -->|DF-1| API
  Operator --> Docker
  Dev([Doc contributor]]) --> Guide
```

## Doc vs code notes

| Topic | Docs / comments | Code reality |
|-------|-----------------|--------------|
| KIND docker socket | `setup-kind-cluster-and-goat.sh` comment mentions extraMounts | `kind-cluster-setup.yaml` has no `extraMounts` — DIND may fail without host socket |
| Architecture detail | “Detailed architecture soon” | Only high-level image in guide |
| Helm2 Tiller | Listed in README scenario 9 | Deprecated; not in setup script |
| AWS EC2 path | Ports 1230–1234 in SG | Ubuntu 16.04 AMI; local scope prefers KIND |

## References

- [THREAT-MODEL.md](./THREAT-MODEL.md) — scope, index, session (architecture baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0`)
- [threat-model/hardened/README.md](./hardened/README.md) — operator safety profile (not in-scenario mitigations)
- Upstream Kubernetes Goat (baseline commit above): `README.md`, `setup-kubernetes-goat.sh`, `access-kubernetes-goat.sh`, `platforms/kind-setup/`, `guide/docs/architecture.md`, `guide/docs/how-to-run/kind.md`
