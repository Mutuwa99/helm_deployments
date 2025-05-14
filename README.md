# By Noble Mutuwa Mulaudzi 

---

# Helm + Argo CD Deployment Pipeline

This repository uses Helm to manage Kubernetes manifests and Argo CD to handle declarative GitOps-based deployments. It supports two environments: `dev` and `prod`. Helm is used to promote reusability of Kubernetes resources—such as deployment manifests, services, and config maps—by templating them. Argo CD then deploys these rendered manifests based on Git branch references, ensuring consistent and traceable environment provisioning.

---

## 🔧 Purpose

* We use **Helm only to template Kubernetes manifests**.
* All actual **deployments are performed by Argo CD including rollbacks through git**.


#### The goal is to maintain a clean, consistent, and reproducible deployment pipeline using Git as the single source of truth, made easier by leveraging Helm's powerful templating capabilities for reusability and manageability of Kubernetes resources.

---

## 🌍 Environments

### 1. **Development (`dev`)**

* Deployed via **Argo CD**.
* Points to the `dev` Git branch.
* Uses `values-dev.yaml` for customizations.
* Ideal for testing and early-stage changes.

### 2. **Production (`prod`)**

* Deployed via **Argo CD**.
* Points to the `main` Git branch.
* Uses `values-prod.yaml` for production-level settings.
* Only changes merged into `main` are deployed here.

---

## 🚀 What Gets Deployed

 What Gets Deployed
* A simple NGINX pod created using Helm templating.
* A ConfigMap that overwrites the default NGINX index page with a custom HTML page.
* A Kubernetes Service to expose the NGINX pod internally within the cluster.
* A ServiceAccount, Role, and RoleBinding to handle scoped access control for the deployed pod.
* The same Helm chart is reused across environments; differences are managed through environment-specific values.yaml files

---

## 📂 Directory Structure

```
.
.
├── charts/
│   └── myapp/
│       ├── templates/
│       │   ├── deployment.yaml          # NGINX Deployment
│       │   ├── configmap.yaml           # Custom index.html ConfigMap
│       │   ├── service.yaml             # Service to expose the pod
│       │   ├── serviceaccount.yaml      # Defines a dedicated ServiceAccount
│       │   ├── role.yaml                # RBAC Role with limited permissions
│       │   └── rolebinding.yaml         # Binds the Role to the ServiceAccount
│       ├── values-dev.yaml              # Dev-specific values
│       ├── values-prod.yaml             # Prod-specific values
│       └── Chart.yaml                   # Helm chart metadata
├── apps/
│   ├── dev-app.yaml                     # Argo CD Application pointing to the dev branch
│   └── prod-app.yaml                    # Argo CD Application pointing to the main branch
└── README.md

```

---

## 🔄 GitOps Flow

### Development

```bash
# Work in dev branch
git checkout dev
# Commit changes
git commit -am "Update nginx config"
# Push to trigger dev Argo CD sync
git push origin dev
```

### Promotion to Production

Here’s the updated **"Promotion to Production"** section with the Pull Request (PR) process included:

---

### 🚀 Promotion to Production

Promotion from the **`dev`** environment to **`prod`** happens through a Git-based workflow using a **Pull Request (PR)**. This ensures that all changes are reviewed before going live.

```bash
# Step 1: Push changes to the dev branch
git add .
git commit -m "Update configuration for dev"
git push origin dev

# Step 2: Open a Pull Request from dev → main
# (This is reviewed and approved by your team)

# Step 3: Once approved, merge the PR into main
# This triggers Argo CD to sync and deploy to prod
```

> ✅ Argo CD watches the `main` branch for production and `dev` branch for development. Merging to `main` automatically updates the production environment.




---

## ✅ Summary

| Tool             | Purpose                                    |
| ---------------- | ------------------------------------------ |
| **Helm**         | Create reusable and customizable templates |
| **Argo CD**      | Declarative GitOps deployments             |
| **Git Branches** | `dev` for testing, `main` for production   |

---


