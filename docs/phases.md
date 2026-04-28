# Phases & Roadmap

The platform was built in **10 phases** (0-9) plus a parallel pre-prod
validation track. Phases 0-7 are complete; Phase 8 (Cluster API) is
near-complete (Metal3 bare-metal provisioning remains); Phase 9 (Test
Automation) released Agentic-TAF v1.0.0 and is now near-complete with
T.10 (LLM-judge expansion) just merged.

## Status table

| Phase | Theme | Status | Highlights |
|-------|-------|--------|-----------|
| **0** | Foundation | Done | 6 GitHub repos, Jenkins JCasC, PostgreSQL HA, NATS JetStream cluster, Flux bootstrap, GHCR image pipeline |
| **1** | Agent core | Done | LangGraph 5-node graph, 30 tools (19 LLM-accessible), 3-tier LLM router, FastAPI app, 488 tests across 5 categories; v0.21.0 SOLID/SoC/KISS refactor cycle complete (10 service classes extracted from monolithic route handlers) |
| **2** | Infrastructure | Done | 6 Kustomize bases, 8 Ansible roles (`common`, `vm-provision`, `bare-metal-provision`, `vm-teardown`, `bare-metal-teardown`, `kubernetes-namespace`, `observability-agent`, `test-harness`) |
| **3** | UI + CI/CD | Done | React/TypeScript dashboard (7 pages), Jenkins shared library (8 steps), GitHub Actions workflows |
| **4** | Reporting & analytics | Done | OpenSearch + ISM lifecycle, SonarQube, kube-prometheus-stack, Fluent Bit, LangFuse self-hosted |
| **5** | Advanced agent | Done | TTL supervisor, heartbeat monitor, orphan detector with 1-hour safety delay, capacity planner, priority preemption, NATS-driven queue processor |
| **6** | Security | Done | RBAC (5 roles × 11 permissions), Sealed Secrets, network policies, K8s audit logging baked into kubeadm bootstrap |
| **7** | Integration testing | Done | E2E provisioning flows, chaos experiments (pod kill, DB failover, NATS partition, Flux suspend), DR runbooks |
| **8** | Cluster API | **Near-complete** | CAPV E2E validated; CNI auto-install via `ClusterResourceSet`; kubeconfig endpoint; force-cleanup for stuck deletions; Ansible VM pipeline. **Remaining: Task 8.9 — Metal3 + NetBox `BareMetalHost` integration.** |
| **9** | Test automation | **Near-complete** | Agentic-TAF v1.0.0 released. T.1-T.10 merged: 277 unit tests, 63 E2E (pytest), 10 BDD (behave), 8 plugins implemented + 1 stub (Appium). T.10 added domain rubrics, shared `llm_judge` fixture, ground-truth tests. F.1-F.4 deferred. |
| Preprod | Validation | Done | 3-node kubeadm cluster operational, 11 HelmReleases reconciled, all P.1-P.13 validation checks passed |

## Phase 9 — Test automation in detail

The framework was an evolution rather than a greenfield build.
**uiXautomation** (PyXTaf v0.5) was renamed to **agentic-taf**, modernized
for Python 3.12+, and extended with new plugins:

- **T.1** — Framework foundation: rename, modernize PyXTaf core, add 5 new
  plugins (Playwright, httpx, WebSocket, LLM-judge, K8s-chaos), CI skeleton
- **T.2** — API tests (21): contract validation, functional, state machine
- **T.3** — UI automation (10): Playwright with engine-agnostic Page Objects
- **T.4** — AI-specific tests (11): LLM-as-judge, adversarial, fallback
- **T.5** — BDD (10 scenarios across 4 feature files via behave)
- **T.6** — Chaos experiments (4)
- **T.7** — Load & performance (4)
- **T.8** — Security tests (8)
- **T.9** — Reporting + CI: JUnit → OpenSearch, coverage → SonarQube,
  AI traces → LangFuse
- **T.10** — LLM-judge expansion: domain rubrics (`GROUND_TRUTH_RUBRIC`,
  `DEGRADED_MODE_RUBRIC`, `ADVERSARIAL_RUBRIC`), shared fixture
  promotion, 5 ground-truth + multi-turn coherence tests

[See agentic-taf on GitHub →](https://github.com/WesleyPeng/agentic-taf){ .md-button .md-button--primary }

## Critical path

```
Phase 0 (Foundation)
    ↓
Phase 1 (Agent Core)
    ↓
Phase 2 (Infrastructure) ───┐
Phase 3 (UI + CI/CD)        │
Phase 4 (Reporting)         │
Phase 5 (Advanced agent)    ├──→ Phase 7 (Integration testing)
Phase 6 (Security)          │       ↓
                            ┘    Phase 8 (Cluster API)
                                    ↓
                                 Phase 9 (Test automation)
```

Phases 4, 5, 6 ran in parallel once Phase 1 was complete. Phase 8 began
after Phase 7 validated the core platform. Phase 9 began after Phase 8
deployed all environment types.

## What's next

### Task 8.9 — Metal3 bare-metal Cluster API

The CAPV side of Phase 8 (vSphere VMs as cluster nodes) is operational.
Metal3 with `BareMetalHost`-driven provisioning for bare-metal clusters
is the only remaining feature task on the active roadmap.
