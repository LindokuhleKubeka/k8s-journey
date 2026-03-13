# CI/CD Pipeline with GitHub Actions → Kubernetes

A work-in-progress CI/CD pipeline for a Flask application using GitHub Actions, Docker, and Kubernetes. The goal: every push to `main` triggers a full pipeline — lint → test → containerise → deploy. Currently implemented through the Docker build stage; Kubernetes deployment manifests are in progress.

> 🚧 **Status: In Progress** — CI pipeline and containerisation are functional. K8s deployment is being completed.

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?logo=flask&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=githubactions&logoColor=white)

---

## Pipeline Overview

```
Push to main
     │
     ▼
┌─────────────┐     ┌─────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   Checkout  │────▶│  Lint/Test  │────▶│  Docker Build    │────▶│  K8s Deploy     │
│   Code      │     │  (pytest)   │     │  & Push to GHCR  │     │  (in progress)  │
└─────────────┘     └─────────────┘     └──────────────────┘     └─────────────────┘
       ✅                  ✅                     ✅                       🚧
```

## What It Does

- **GitHub Actions workflow** triggers on every push and pull request to `main`
- **Docker image** is built and tagged with the commit SHA for traceability
- **Kubernetes manifests** (`Deployment` + `Service`) handle rolling updates with zero-downtime strategy
- **Health checks** via readiness and liveness probes ensure failed deployments don't serve traffic

## Project Structure

```
ci-cd-flask/
├── app/
│   ├── __init__.py
│   └── routes.py
├── tests/
│   └── test_routes.py
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
├── Dockerfile
├── requirements.txt
└── .github/
    └── workflows/
        └── deploy.yml
```

## Key Files

### `.github/workflows/deploy.yml`
The pipeline definition — triggers, build steps, and kubectl apply commands.

### `k8s/deployment.yaml`
Kubernetes Deployment with rolling update strategy and resource limits defined.

### `Dockerfile`
Multi-stage build keeping the final image lean — build dependencies are not included in the runtime image.

## Running Locally

**Prerequisites:** Docker, Minikube, kubectl

```bash
# Start Minikube
minikube start

# Apply manifests
kubectl apply -f k8s/

# Check deployment
kubectl get pods
kubectl get services

# Access the app
minikube service flask-service
```

## What I Learned

- Structuring GitHub Actions workflows for CI/CD separation (build vs deploy jobs)
- Using commit SHAs as image tags to maintain deployment traceability
- Configuring Kubernetes readiness/liveness probes to prevent bad deployments from receiving traffic
- Rolling update strategies to achieve zero-downtime deploys

## Improvements Planned

- [ ] Add staging environment with branch-based deployments
- [ ] Integrate Trivy for container image vulnerability scanning
- [ ] Push to a cloud registry (ECR or ACR) instead of GHCR
- [ ] Add Helm chart for configurable deployments

---

*Part of my DevOps learning journey — see also [helm-nodejs](https://github.com/LindokuhleKubeka/helm-nodejs) and [terraform-azure-vm](https://github.com/LindokuhleKubeka/terraform-azure-vm).*
