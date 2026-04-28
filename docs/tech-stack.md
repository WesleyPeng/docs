# Technology Stack

| Layer | Components |
|-------|-----------|
| **LLM agent** | LangGraph 0.2+, LangChain 0.3+, langchain-openai, langchain-anthropic |
| **Web framework** | FastAPI, Pydantic v2, uvicorn, asyncpg, websockets |
| **State store** | PostgreSQL 18 (HA: 1 primary + 1 replica), `langgraph-checkpoint-postgres` |
| **Event bus** | NATS JetStream (3-replica cluster, 3 streams: RESERVATIONS, PIPELINES, INFRASTRUCTURE) |
| **GitOps** | Flux v2 (source-controller, kustomize-controller, helm-controller, notification-controller) |
| **Provisioning** | Kustomize, Helm, Cluster API (CAPV for vSphere; Metal3 planned), Ansible (containerized as a K8s Job) |
| **Container registry** | GitHub Container Registry (`ghcr.io/<org>/...`) |
| **Secrets** | Sealed Secrets — encrypted at rest in Git, decrypted in-cluster by the controller |
| **Web UI** | React 19, TypeScript, Vite, Ant Design |
| **CI/CD** | Jenkins (with kubernetes-plugin, JCasC, shared library), GitHub Actions |
| **Code quality** | SonarQube Community 10.x, flake8, mypy, ESLint, Prettier |
| **Test automation** | [agentic-taf](https://github.com/WesleyPeng/agentic-taf) (PyXTaf evolved) — pytest, behave, Playwright, httpx, websockets, paramiko, langchain-openai, kubernetes |
| **Metrics** | Prometheus + Grafana via kube-prometheus-stack |
| **Logs** | OpenSearch + OpenSearch Dashboards (single-node), Fluent Bit DaemonSet |
| **LLM observability** | LangFuse self-hosted (chart 1.5.x, app 3.162.x) — bundled ClickHouse + Valkey + MinIO + external PostgreSQL |
| **Distributed tracing** | OpenTelemetry SDK + Jaeger backend on OpenSearch |
| **Bare-metal/VM** | NetBox (CMDB), vSphere REST `/api/` (vCenter 7.0+), Ansible vSphere collection, Ansible IPMI module |

## Cluster topology (preprod)

- 3-node Kubernetes cluster (kubeadm)
- Ubuntu 24.04.4 LTS, containerd 1.7.x, kubeadm v1.31.14
- Calico CNI (default), Flannel CNI (alternative for clusters without
  IPAM)
- Rancher local-path-provisioner as the default StorageClass (since
  kubeadm has no built-in CSI)
- NGINX Ingress as a `DaemonSet` with `hostPort: true` (standard for
  on-prem clusters without a cloud LB)
- cert-manager 1.20.x with an in-cluster CA `Issuer` for service TLS

## Image versions (current)

| Image | Tag |
|-------|-----|
| `ghcr.io/wesleypeng/agentic-qa-agent` | v0.21.0 |
| `ghcr.io/wesleypeng/qa-dashboard` | v0.9.1 |
| `ghcr.io/wesleypeng/ansible-runner` | v0.3.5 |
| `ghcr.io/wesleypeng/agentic-taf` | v1.0.0 |

## Deployment surface

| Service | Port | Notes |
|---------|------|-------|
| QA Dashboard | 80/443 | NGINX Ingress with TLS via cert-manager |
| Agent REST + WebSocket | 8000 | Cluster-internal `Service`; reached through Ingress for browser/SSO redirect |
| Jenkins | 8080 | TCP passthrough |
| OpenSearch Dashboards | 5601 | TCP passthrough |
| Prometheus / Grafana | 3000 | TCP passthrough |
| SonarQube | 9000 | TCP passthrough |
| LangFuse | 3100 | TCP passthrough |

## What it doesn't depend on

- No cloud-provider lock-in: works with any kubeadm-installable
  Kubernetes (vSphere, bare-metal, public cloud...)
- No external secret manager: Sealed Secrets keep encrypted Secret
  manifests in Git
- No managed database service: PostgreSQL HelmRelease in-cluster
- No proprietary CI: Jenkins (open-source) is primary; any CI can call
  the agent's REST API
