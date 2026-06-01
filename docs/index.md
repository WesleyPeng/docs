---
title: Home
---

# Agentic QA Platform

**An LLM-powered platform that automates test-environment provisioning across
Kubernetes, bare metal, and virtual machines — turning a chat conversation into
a fully reconciled environment ready for CI/CD pipelines and human testers.**

[Architecture](architecture.md){ .md-button .md-button--primary }
[Test Automation Framework](test-automation.md){ .md-button }
[GitHub: Agentic-TAF](https://github.com/WesleyPeng/agentic-taf){ .md-button }

---

## What it does

Talk to the agent. Get an environment.

- **"I need a 1-CPU 2-GB Kubernetes namespace for the payments team for 4 hours"**
  → LangGraph agent classifies the intent, picks a tier-1 LLM, validates the
  request against team quotas, generates a Kustomize overlay, commits it to
  Git, lets Flux reconcile, and returns a `READY` reservation with the
  endpoints the user can use.
- **"Provision a brand-new K8s cluster on vSphere with 1 control-plane and 2
  workers"** → Same conversational flow, but now the agent emits a Cluster API
  + CAPV manifest, the IPAM controller assigns IPs, kubeadm bootstraps the
  control plane, Calico installs via `ClusterResourceSet`, and the user gets
  a downloadable kubeconfig.
- **"Spin me up a VM from the `ubuntu-2404-kube-v1.31.14` template"** → A
  K8s Job runs the `ansible-runner` image, clones the template on vCenter
  via the `/api/` endpoints, and reports back via NATS JetStream.

All actions are tracked end-to-end via correlation IDs through Prometheus,
LangFuse, OpenSearch, and SonarQube.

---

## Six repositories, one platform

The platform is split across six focused repositories:

<div class="grid cards" markdown>

-   :material-book-open-page-variant: **agentic-qa-platform** *(private)*

    ---

    Design authority — architecture documentation, implementation plans,
    and deployment runbooks.

-   :material-robot: **agentic-qa-agent** *(private)*

    ---

    Python LangGraph agent. 5-node graph (router → planner → executor →
    reflector → responder), 32 LangChain tools, 3-tier LLM routing, FastAPI
    REST + WebSocket interface.

-   :material-server: **infra-provisioning** *(private)*

    ---

    GitOps source: Flux Kustomizations, HelmReleases, Ansible roles for
    bare-metal/VM, Cluster API templates for vSphere, and the
    `ansible-runner` container image.

-   :material-monitor-dashboard: **qa-dashboard** *(private)*

    ---

    React + TypeScript web UI. 7 pages: Dashboard, Chat, Environments,
    Test Results, Reports, Analytics, Login.

-   :material-pipe: **jenkins-pipelines** *(private)*

    ---

    Groovy shared library and Jenkinsfile templates that call the agent's
    REST API to request and release environments from CI pipelines.

-   :material-shield-check: **agentic-taf** *(public — [GitHub ↗](https://github.com/WesleyPeng/agentic-taf))*

    ---

    Test Automation Framework. Plugin-based (Selenium, Playwright, httpx,
    requests, websockets, Paramiko, LLM-judge, K8s chaos), Python 3.12,
    Apache-2.0. Released as v1.0.0.

</div>

!!! info "Why are five repos private?"

    The five private repositories contain operational details
    (vCenter endpoints, IPAM ranges, LLM gateway URLs, and
    cluster-specific Sealed Secrets) that aren't safe to publish.
    The architecture, design patterns, and cross-repo coordination
    techniques are all described publicly on this site. The framework
    that exercises the platform — Agentic-TAF — is fully open source.

---

## Status snapshot

| Phase | Theme | Status |
|-------|-------|--------|
| 0 | Foundation (repos, Jenkins, PostgreSQL, NATS, Flux, GHCR) | Done |
| 1 | Agent core (LangGraph, 32 tools, 3-tier LLM, 739 tests; v0.22.27 with SOLID refactors + Phase 10) | Done |
| 2 | Infrastructure (Kustomize, Ansible, Sealed Secrets) | Done |
| 3 | Web UI + CI/CD shared library | Done |
| 4 | Reporting (OpenSearch, SonarQube, Prometheus, LangFuse) | Done |
| 5 | Advanced agent (TTL, orphans, capacity, preemption) | Done |
| 6 | Security (RBAC, mTLS, K8s audit) | Done |
| 7 | Integration testing (E2E, chaos, load, DR) | Done |
| 8 | Cluster API (CAPV done, Metal3 remaining) | **Near-complete** |
| 9 | Test automation (Agentic-TAF v1.0.0, T.1-T.10) | **Near-complete** |

[See full phase status →](phases.md)

---

## Highlights

- **3-tier LLM routing** — local LLM (SSO) → OpenRouter → Anthropic Claude,
  with automatic fallback. Same `.bind_tools()` API everywhere.
- **13-state environment lifecycle** — REQUESTED → VALIDATING → PROVISIONING
  → READY → IN_USE → RELEASING → DEPROVISIONING → RECLAIMED, plus REJECTED
  / FAILED / RELEASE_FAILED / TEARDOWN_FAILED / QUEUED.
- **GitOps-everything** — agent commits to `infra-provisioning` repo; Flux
  reconciles; live cluster is the eventual consistency frontier. `prune: true`
  for environments.
- **Sealed Secrets, not external secret managers** — encrypted at rest in
  Git, decrypted in-cluster by the controller; no external secret-manager
  dependency.
- **Containerized Ansible** — bare-metal and VM provisioning runs as a K8s
  Job (`ghcr.io/wesleypeng/ansible-runner`) with the Ansible vSphere
  collection.
- **LLM observability via LangFuse** — every prompt, response, token count,
  latency tracked with `env_id` correlation.

[Read the architecture →](architecture.md)
