# Resources

## Public

- **[agentic-taf on GitHub](https://github.com/WesleyPeng/agentic-taf)**
  &mdash; the test automation framework. Apache-2.0, Python 3.12+, 293 unit
  tests, 9 plugins.
- **[Architecture diagram (SVG)](https://raw.githubusercontent.com/WesleyPeng/agentic-taf/main/architecture-diagram.svg)**
  &mdash; the multi-layer plugin architecture.
- **[Implementation plan](https://github.com/WesleyPeng/agentic-taf/blob/main/docs/implementation-plan.md)**
  &mdash; T.1-T.10 task tracker for the framework.
- **[Container image on GHCR](https://github.com/WesleyPeng/agentic-taf/pkgs/container/agentic-taf)**
  &mdash; `ghcr.io/wesleypeng/agentic-taf:v1.0.0`.

## Legacy report

The original SW QA Automation Recommendations Assessment Report
(Word-export HTML) is preserved at
[**/docs/assessment-report/**](assessment-report/index.html). The
TAF implementation slide deck and PDF are available there as well.

## Reading order

If you're new to the platform, this is a good progression:

1. [**Home**](index.md) &mdash; tagline + what the platform does
2. [**Architecture**](architecture.md) &mdash; layers, GitOps reconciliation,
   state machine, LLM routing, RBAC
3. [**Phases & Roadmap**](phases.md) &mdash; status of each phase, what's
   done, what's deferred
4. [**Test Automation**](test-automation.md) &mdash; the open-source
   `agentic-taf` framework
5. [**Tech Stack**](tech-stack.md) &mdash; component versions and choices
6. [**About**](about.md) &mdash; how to access private repositories

## External technology docs

- [LangGraph](https://langchain-ai.github.io/langgraph/) &mdash; the
  graph-based agent framework
- [Cluster API](https://cluster-api.sigs.k8s.io/) &mdash; declarative
  Kubernetes cluster lifecycle (CAPI)
- [CAPV](https://github.com/kubernetes-sigs/cluster-api-provider-vsphere)
  &mdash; vSphere infrastructure provider for CAPI
- [Metal3](https://metal3.io/) &mdash; bare-metal infrastructure provider
  (planned for Task 8.9)
- [Flux](https://fluxcd.io/) &mdash; GitOps for Kubernetes
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets)
  &mdash; encrypted Secrets in Git
- [LangFuse](https://langfuse.com/) &mdash; LLM observability and tracing
- [NATS JetStream](https://docs.nats.io/nats-concepts/jetstream) &mdash;
  the persistence layer for the event bus
