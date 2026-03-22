# kubernetes-lab

A comprehensive Kubernetes learning and reference repository covering the full ecosystem — from basic Pods through production deployments on GCP/AWS, with complete observability (ELK, Prometheus/Grafana), Helm charts, RBAC, autoscaling, and advanced workload types.

## Repository Structure

```
kubernetes-lab/
├── local/             # Manifests for local/minikube clusters
│   └── archive/       # Earlier iterations (Pods, ReplicaSets)
├── gcp/               # Production-ready GKE configurations
├── other-objects/     # Ingress, Secrets, ConfigMaps, HPA
├── helm-chart-play/   # Helm chart templating
└── resource/          # Chapter-by-chapter learning path
```

## Primary Application — Fleetman

All manifests deploy **Fleetman**, a fleet-management/vehicle-tracking microservice system:

| Service | Image | Role |
|---------|-------|------|
| **frontend** | `k8s-fleetman-webapp-angular` | Angular web interface |
| **api-gateway** | `k8s-fleetman-api-gateway` | REST API layer |
| **position-tracker** | `k8s-fleetman-position-tracker` | Processes vehicle position data |
| **position-simulator** | `k8s-fleetman-position-simulator` | Generates mock position data |
| **queue** | `k8s-fleetman-queue` (ActiveMQ) | Message broker |
| **MongoDB** | `mongo:3.6.5-jessie` | Data persistence |

## Local Environment (`/local`)

Deployment manifests for minikube or local node clusters.

| File | Contents |
|------|----------|
| `workload.yaml` | 5 Deployments (queue, position-sim, position-track, api-gate, frontend) |
| `services.yaml` | 4 Services — NodePort for webapp (30023) and queue (30010), ClusterIP for internal |
| `mongo.yaml` | MongoDB Deployment + Service |
| `storage.yaml` | PersistentVolume (hostPath) + PersistentVolumeClaim |

**Archive:** Earlier learning iterations — raw Pods (`pods.yaml`), ReplicaSets (`replicaset.yaml`), first Deployments (`deployments.yaml`), MySQL networking example (`networking.yaml`).

## GCP / GKE Production (`/gcp`)

Cloud-optimised configurations with resource management and observability.

| File | What it adds over local |
|------|------------------------|
| `workload.yaml` | CPU/memory requests and limits, readiness probes, 2 frontend replicas |
| `services.yaml` | LoadBalancer (webapp), ClusterIP (queue) — no NodePorts |
| `mongo.yaml` | Resource constraints (500m CPU, 500Mi memory) |
| `storage.yaml` | StorageClass `kubernetes.io/gce-pd` (pd-standard), 2Gi dynamic provisioning |
| `elastic-stack.yaml` | Complete ELK stack — Fluentd DaemonSet, Elasticsearch StatefulSet (2 replicas, 13Gi), Kibana, RBAC |
| `fluentd-config.yaml` | Multi-part ConfigMap: container log parsing, system component logs, Elasticsearch output with buffering |
| `crds.yaml` | Prometheus-related Custom Resource Definitions |
| `monitoring.yaml` | Prometheus Operator, AlertManager, ServiceMonitors |

## Other Kubernetes Objects (`/other-objects`)

| File | Resource | Details |
|------|----------|---------|
| `ingress.yaml` | Ingress | Layer 7 routing — `fleetman.com` → webapp, `queue.fleetman.com` → queue (nginx-ingress) |
| `secrets.yaml` | Secret | Base64-encoded access key and secret key |
| `db-configmap.yaml` | ConfigMap | Database URL and password |
| `autoscale-rules.yaml` | HPA v2 | api-gateway: 1–4 replicas, CPU metric target |

## Helm Chart (`/helm-chart-play`)

A demonstration Helm chart for templated deployments.

- `Chart.yaml` — chart name, version 0.1.0, app version 0.1.2-alpha
- `values.yaml` — `numberOfWebAppReplicas: 3`, `repoName` variable
- `templates/play.yaml` — Deployment + Service using `{{ .Values.* }}` and Helm functions (`upper`)

## Learning Path (`/resource`)

Chapter-by-chapter Kubernetes progression:

| Chapter | Topic | Key Files |
|---------|-------|-----------|
| 5 | Pods | `first-pod.yaml` |
| 6 | Services | Pod + NodePort Service |
| 7 | Exercise | Practical exercises |
| 8 | ReplicaSets | ReplicaSet with selectors |
| 9 | Deployments | Deployment wrapping ReplicaSets |
| 10 | Networking | Network policies, service routing |
| 11 | Microservices | Full Fleetman architecture (5 workloads + services), multi-arch variant |
| 12 | Persistence | MongoDB + PV/PVC + stateful workloads |
| 13 | AWS | EBS volumes, AWS storage, AWS-optimised deployments |
| 15 | ELK | Elasticsearch + Logstash + Kibana pipeline |
| 16 | Monitoring | Full Prometheus stack (Operator, Grafana, node exporter, AlertManager, ServiceMonitors) |
| 17 | Alerts | AlertManager routing configuration |

### Going Further

| Topic | What it covers |
|-------|---------------|
| **ConfigMaps** | `aws-credentials.yaml`, `database-config.yaml` |
| **HPA** | Metric-based horizontal pod autoscaling rules |
| **Helm** | `fleetman-helm-demo-start.yaml` |
| **Ingress** | Basic routing, public exposure, HTTPS/TLS (`ingress-secure.yaml`) |
| **Other Workloads** | Job, CronJob, StatefulSet (`mongo-replicated.yaml`), DaemonSet |
| **RBAC** | Role + RoleBinding example (`rules-for-new-joiners.yaml`) |

## Kubernetes Resources Demonstrated

**Workloads:** Pod, Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob

**Services & Networking:** ClusterIP, NodePort, LoadBalancer, Ingress (nginx)

**Storage:** PersistentVolume, PersistentVolumeClaim, StorageClass (hostPath, GCE PD, AWS EBS)

**Configuration:** ConfigMap, Secret, environment variables

**Observability:** Prometheus + Grafana (metrics), Elasticsearch + Kibana (logs), Fluentd (collection), AlertManager (alerting)

**Security & Governance:** RBAC (ServiceAccount, Role, RoleBinding, ClusterRole, ClusterRoleBinding), HPA, resource requests/limits, readiness probes

**Packaging:** Helm charts, Custom Resource Definitions (CRDs)

## Cloud Providers

- **Local** — minikube / bare-metal with hostPath volumes
- **GCP (GKE)** — GCE Persistent Disk, LoadBalancer Services, full ELK + Prometheus stack
- **AWS (EKS)** — EBS volumes, AWS-specific storage configurations
