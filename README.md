# 🎓 Homework Tracker — Cloud-Native DevOps Capstone Project

<p align="center">
  <img src="https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue?logo=github-actions&logoColor=white" alt="CI/CD" />
  <img src="https://img.shields.io/badge/Kubernetes-K3s%20%7C%20Kind-326CE5?logo=kubernetes&logoColor=white" alt="Kubernetes" />
  <img src="https://img.shields.io/badge/Helm-v3.14+-0F1689?logo=helm&logoColor=white" alt="Helm" />
  <img src="https://img.shields.io/badge/Docker-Multi--Stage-2496ED?logo=docker&logoColor=white" alt="Docker" />
  <img src="https://img.shields.io/badge/GitOps-Argo%20CD-EF6B3B?logo=argo&logoColor=white" alt="GitOps" />
  <img src="https://img.shields.io/badge/IaC-Terraform-7B42BC?logo=terraform&logoColor=white" alt="Terraform" />
  <img src="https://img.shields.io/badge/Security-DevSecOps%20Gates%20Passed-brightgreen?logo=trivy&logoColor=white" alt="DevSecOps" />
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License" />
</p>

---

## 📌 Executive Summary

**Homework Tracker** is an enterprise-grade, cloud-native DevOps capstone application designed to track, schedule, and grade course assignments with real-time status progression. Built from the ground up to showcase full-lifecycle DevOps engineering, the project demonstrates:

* **Microservices Architecture**: React 18 + Vite frontend, Python FastAPI REST backend, and PostgreSQL 16 relational database with Alembic schema versioning.
* **Modern Containerization**: Secure multi-stage, non-root Docker builds yielding minimal attack surface and lightweight runtime footprints.
* **End-to-End DevSecOps Pipeline**: Automated testing, static application security testing (Bandit), software composition analysis (pip-audit), secret detection, container image scanning (Trivy), and automated release gating via GitHub Actions.
* **Production Packaging with Helm 3**: Fully parameterized Kubernetes charts supporting configurable replicas, dynamic storage provisioning, Horizontal Pod Autoscaling (HPA), and lifecycle probes (startup, readiness, liveness).
* **GitOps Continuous Reconciliation**: Declarative application state management using Argo CD with automated self-healing and zero configuration drift.
* **Infrastructure as Code (IaC)**: Automated cloud infrastructure provisioning on Microsoft Azure via Terraform with isolated virtual networks, network security groups, and automated bootstrap via cloud-init.
* **Full-Stack Observability**: Metrics scraping via Prometheus, custom Grafana dashboards, alerting rules, and health endpoints.

---

## 🏛️ System Architecture

### 1. High-Level Runtime Topology
```mermaid
flowchart TD
    subgraph Client["Public Access"]
        User["Client Browser"]
    end

    subgraph IngressLayer["Ingress & Routing Layer"]
        Ingress["Ingress Controller (Traefik / Nginx)"]
    end

    subgraph KubernetesCluster["Kubernetes Cluster (K3s / Kind)"]
        subgraph FrontendTier["Frontend Tier"]
            F_SVC["Frontend Service (ClusterIP)"]
            F_POD1["React Pod 1 (Nginx Alpine)"]
            F_POD2["React Pod 2 (Nginx Alpine)"]
        end

        subgraph BackendTier["Backend API Tier"]
            B_SVC["Backend Service (ClusterIP)"]
            B_POD1["FastAPI Pod 1 (Non-root)"]
            B_POD2["FastAPI Pod 2 (Non-root)"]
            HPA["Horizontal Pod Autoscaler (CPU >= 50%)"]
        end

        subgraph DataTier["Data Persistence Tier"]
            DB_SVC["Postgres Service (ClusterIP:5432)"]
            DB_POD["PostgreSQL 16 Pod"]
            PVC["PersistentVolumeClaim (Standard SC)"]
            PV["Persistent Volume (/var/lib/postgresql/data)"]
        end

        subgraph MonitoringTier["Observability Stack"]
            PROM["Prometheus Server"]
            GRAF["Grafana Dashboards"]
            SM["ServiceMonitor / HTTP Probes"]
        end
    end

    User -->|HTTP /| Ingress
    Ingress -->|Path /| F_SVC
    Ingress -->|Path /api| B_SVC
    F_SVC --> F_POD1 & F_POD2
    B_SVC --> B_POD1 & B_POD2
    HPA -.->|Scale 2-5 Replicas| BackendTier
    B_POD1 & B_POD2 -->|SQL Queries| DB_SVC
    DB_SVC --> DB_POD
    DB_POD --- PVC --- PV
    SM -->|Scrape :8000/metrics| B_SVC
    PROM --> SM
    GRAF -->|PromQL Queries| PROM
```

---

### 2. DevSecOps & GitOps Delivery Pipeline
```mermaid
flowchart LR
    Dev["Developer Git Push"] -->|main branch| GHA["GitHub Actions Workflow"]

    subgraph CI["Continuous Integration & DevSecOps Gate"]
        UT["Unit Tests (pytest)"]
        SAST["SAST (Bandit)"]
        SCA["SCA (pip-audit)"]
        Secret["Secret Scan (Trivy)"]
        DockerBuild["Multi-Stage Docker Build"]
        TrivyImage["Container Scan (Trivy)"]
        Gate{"Security Gate: 0 Critical / High?"}
    end

    subgraph CD["Registry & Delivery"]
        GHCR["GitHub Container Registry (GHCR)"]
        Kind["Disposable Kind K8s Cluster"]
        HelmLint["Helm Chart Lint & Test Deploy"]
        HPATest["Automated Load & HPA Scaling Test"]
        GitOpsTest["Argo CD Sync Verification"]
    end

    subgraph Cloud["Production Cloud (Azure)"]
        Terraform["Terraform Apply"]
        VM["Ubuntu VM with K3s"]
        ArgoCD["Argo CD Controller"]
        LiveApp["Production Capstone Application"]
    end

    Dev --> GHA
    GHA --> UT --> SAST --> SCA --> Secret --> DockerBuild --> TrivyImage --> Gate
    Gate -->|Passed| GHCR
    GHCR --> Kind --> HelmLint --> HPATest --> GitOpsTest
    GitOpsTest -->|Validated Release| Cloud
    Terraform --> VM --> ArgoCD -->|Continuous Reconciliation| LiveApp
```

---

## 🛠️ Technology Stack Matrix

| Domain | Technology / Tool | Version | Purpose |
| :--- | :--- | :--- | :--- |
| **Frontend** | React, Vite, Nginx | React 18, Node 22 | Modern responsive SPA dashboard, task status boards |
| **Backend API** | Python, FastAPI, Pydantic | Python 3.12 | Asynchronous RESTful API, input validation, CORS handling |
| **Database** | PostgreSQL, SQLAlchemy, Alembic | Postgres 16 | Relational storage, declarative ORM, migration tracking |
| **Containerization** | Docker Engine, Docker Compose | Compose v2 | Multi-stage caching, non-root execution, container isolation |
| **Kubernetes** | K3s, Kind, Kubernetes API | v1.30+ | Container orchestration, self-healing, Service discovery |
| **Package Manager** | Helm 3 | v3.14+ | Templated manifests, release revisions, rollback management |
| **Continuous Integration** | GitHub Actions | Ubuntu 24.04 | Automated test runners, release packaging, artifact publishing |
| **Security / DevSecOps**| Bandit, pip-audit, Trivy, Gitleaks | Latest | SAST, dependency analysis, secret scan, CVE gating |
| **GitOps** | Argo CD Core | v2.10+ | Declarative Git-as-source-of-truth automated delivery |
| **Infrastructure as Code**| Terraform, AzureRM Provider | v1.8+ | Cloud compute, networking, security rules, and teardown |
| **Observability** | Prometheus, Grafana | Latest | Metrics scraping, latency/error tracking, visual dashboards |

---

## 🚀 Quick Start Guide

### Prerequisites
* **Docker Engine** (v24.0+) with **Docker Compose v2**
* **cURL** and modern web browser

### Option 1: Docker Compose (Recommended for Local Evaluation)
Launch the complete stack (Frontend, Backend, Database) with a single command:

```bash
# 1. Clone repository
git clone https://github.com/Rabari9999/devops-homework-tracker.git
cd devops-homework-tracker

# 2. Start services with auto-build and healthcheck verification
docker compose up -d --build --wait

# 3. Verify container statuses
docker compose ps
```

#### Access Points:
| Service | URL / Port | Credentials / Notes |
| :--- | :--- | :--- |
| **Web Dashboard** | [http://localhost:3000](http://localhost:3000) | Interactive assignment tracking interface |
| **Interactive API Docs**| [http://localhost:8000/docs](http://localhost:8000/docs) | Swagger UI for exploring REST endpoints |
| **Readiness Probe** | [http://localhost:8000/ready](http://localhost:8000/ready) | Verifies backend database connectivity |
| **Liveness Probe** | [http://localhost:8000/health](http://localhost:8000/health) | Container health endpoint |

```bash
# Tear down services (preserving database volume)
docker compose down

# Full cleanup (including database volume)
docker compose down -v
```

---

### Option 2: Kubernetes Deployment with Helm 3

Deploy directly to your local Minikube or Kind cluster:

```bash
# 1. Add required repositories and build local images
helm repo add traefik https://traefik.github.io/charts
helm repo update

# 2. Lint Helm chart
helm lint ./helm/taskboard

# 3. Install or upgrade release in dedicated namespace
helm upgrade --install homework ./helm/taskboard \
  --namespace production \
  --create-namespace \
  --values ./helm/taskboard/values-prod.yaml \
  --wait --timeout 300s

# 4. Verify Pods, Services, PVCs, and HPA
kubectl -n production get all,pvc,hpa
```

---

## 📡 RESTful API Reference

The backend provides complete CRUD capabilities for course assignments. Complete interactive schema docs are available at `/docs`.

| Method | Endpoint | Description | Request Body / Query | Status Code |
| :--- | :--- | :--- | :--- | :--- |
| `GET` | `/api/tasks` | List all tasks / assignments | `?session=X&priority=HIGH` | `200 OK` |
| `POST` | `/api/tasks` | Create a new task | JSON task payload | `201 Created` |
| `GET` | `/api/tasks/{id}` | Retrieve specific task details | None | `200 OK` / `404` |
| `PUT` | `/api/tasks/{id}` | Update task status or metadata | JSON update payload | `200 OK` / `404` |
| `DELETE`| `/api/tasks/{id}` | Remove a task | None | `204 No Content` |
| `GET` | `/ready` | Database readiness check | None | `200 OK` / `503` |
| `GET` | `/health` | Application liveness probe | None | `200 OK` |
| `GET` | `/metrics` | Prometheus metrics endpoint | None | `200 OK` |

#### Sample Task Creation Request:
```bash
curl -X POST http://localhost:8000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Session 21 Capstone Deployment",
    "description": "Complete production verification on K8s",
    "session": "Session 21",
    "assignee": "Krish",
    "priority": "HIGH",
    "status": "IN_PROGRESS",
    "due_date": "2026-10-16"
  }'
```

---

## 🔒 DevSecOps & Security Governance

Security is shifted strictly left with zero tolerance for high or critical vulnerabilities before artifact generation:

| Security Domain | Scanning Tool | Policy / Threshold | Action on Failure |
| :--- | :--- | :--- | :--- |
| **SAST** | Bandit | Severity: Medium/High | Fails CI pipeline |
| **SCA** | pip-audit & npm audit | CVSS >= 7.0 (High/Critical) | Fails CI pipeline |
| **Secret Scanning** | Trivy (FS Secret Scanner) | Any detected API key / token | Fails CI pipeline |
| **Container Images** | Trivy Container Scanner | Severity: HIGH,CRITICAL | Blocks GHCR publishing |
| **Container Runtime** | Non-Root User Execution | UID 10001 (backend) / non-root (frontend) | Enforced in Dockerfiles |

---

## 📈 Observability & Autoscaling (HPA)

### 1. Prometheus Telemetry
The application exposes Prometheus-compliant metrics and supports automated scraping:
* CPU and Memory utilization per container.
* HTTP request rates, response latency percentiles ($p50$, $p95$, $p99$), and HTTP error codes.
* Active database connection pool metrics.

### 2. Horizontal Pod Autoscaling (HPA)
The backend includes an active `HorizontalPodAutoscaler` resource:
* **Metric**: Average CPU utilization $\ge 50\%$.
* **Replica Bounds**: Minimum 2 Pods, Maximum 5 Pods.
* **Scale-down Stabilization**: 30-second window to prevent thrashing.
* **Load Test Validation**: Verified using custom ApacheBench / HTTP load scripts in `scripts/load-test.sh`.

---

## ☁️ Cloud Infrastructure as Code (Terraform)

Located in [`terraform/`](terraform/):
* **Provider**: `hashicorp/azurerm`
* **Provisioned Resources**:
  * Azure Resource Group (`rg-homework-tracker`)
  * Virtual Network (`10.21.0.0/16`) & Subnet (`10.21.1.0/24`)
  * Network Security Group with strict port 80/443 ingress and restricted SSH admin access
  * Ubuntu 24.04 LTS Compute Instance running lightweight K3s Kubernetes
* **Automated Cloud-Init**: Installs K3s, Helm, Prometheus, and Traefik Ingress automatically upon boot.
* **Full Teardown**: Cleanly destroyed with `terraform destroy -auto-approve`.

---

## 📂 Repository Directory Layout

```text
├── .github/
│   └── workflows/ci-cd.yml      # Automated CI/CD + DevSecOps Pipeline
├── backend/
│   ├── alembic/                 # Database migrations (PostgreSQL schemas)
│   ├── app/                     # FastAPI core, database ORM, routers, schemas
│   ├── tests/                   # Pytest test suite (11 unit and API tests)
│   └── Dockerfile               # Multi-stage non-root Python build
├── frontend/
│   ├── src/                     # React 18 dashboard, CSS styling, components
│   ├── nginx.conf               # Hardened reverse-proxy Nginx config
│   └── Dockerfile               # Multi-stage build (Node -> Nginx Alpine)
├── helm/
│   └── taskboard/               # Production Helm 3 chart (Templates, Values)
├── gitops/                      # Declarative Argo CD Application manifests
├── k8s/                         # Standalone Kubernetes manifests (Namespaces, RBAC)
├── monitoring/                  # Prometheus and Grafana values and alert configs
├── terraform/                   # Azure Infrastructure as Code configurations
├── scripts/                     # Load testing, verification, and diagnostics scripts
├── docs/                        # In-depth architectural and deployment runbooks
└── screenshots/                 # Captured terminal outputs and execution evidence
```

---

## 📜 Documentation Links

* [**Deployment Guide**](docs/deployment.md): Detailed production Kubernetes and Cloud VM setup.
* [**Local Development Guide**](docs/development.md): Environment variables, database setup, and testing.
* [**Verification Matrix**](docs/verification.md): Complete rubric checklist and test output artifacts.
* [**Troubleshooting Runbook**](docs/troubleshooting.md): Diagnosis workflows for common Kubernetes failure scenarios.
* [**Submission Details**](docs/course/submission.md): Course requirements and delivery verification.

---

## 👤 Author & Maintainer

* **Author**: Krish Rabari
* **GitHub**: [@Rabari9999](https://github.com/Rabari9999)
* **Email**: [rabarikrishramjibhai2905@gmail.com](mailto:rabarikrishramjibhai2905@gmail.com)
* **Course**: DevOps Heroes Advanced Curriculum
