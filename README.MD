# GitOps Infrastructure: AI Task Platform

This repository acts as the single source of truth for the Kubernetes infrastructure of the AI Task Platform. It is designed to be managed entirely via a GitOps workflow using Argo CD.

## Infrastructure Stack
* **Orchestration:** Kubernetes (Local Docker Desktop)
* **Continuous Delivery:** Argo CD
* **Continuous Integration:** GitHub Actions (Automated image tagging)
* **Workloads:** 
  * Node.js Backend API (Deployment, Service)
  * React Frontend (Deployment, Service)
  * Python Worker (ReplicaSet, scaled to 3 pods)
  * MongoDB & Redis (Stateful data)

## The GitOps Workflow
1. Code is merged into the `main` branch of the Application repository.
2. A GitHub Action automatically builds the Docker images, pushes them to Docker Hub, and updates the image tags in this infrastructure repository.
3. Argo CD detects the configuration drift in this repository and automatically synchronizes the live Kubernetes cluster with zero downtime.

## Cluster Setup & Disaster Recovery
To deploy this entire architecture from scratch to a fresh Kubernetes cluster:

```bash
# 1. Install Argo CD
kubectl create namespace argocd
kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)

# 2. Trigger the GitOps Sync
kubectl apply -f argocd/application.yaml

# 3. Access the Argo CD Dashboard
kubectl port-forward svc/argocd-server -n argocd 8443:443
# Go to https://localhost:8443