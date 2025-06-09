# CI/CD Pipeline with GitHub Actions & ArgoCD 🚀

This Repo Demonstrate the CI/CD pipeline using GitHub Actions, Docker, and ArgoCD — including Dockerfile creation guidance and CLI setup in WSL (Windows Subsystem for Linux).


## 🛠️ Pre-requisites

Before running the pipeline, ensure your project contains:

- A **Dockerfile** at project root (builds the application container).
- Kubernetes manifests under `manifest/`, including `deployment.yaml`.
- A `.github/workflows/ci-cd.yml` file (see stages below).
- Required **GitHub Secrets** for Docker and ArgoCD access.


## ⚙️ Pipeline Overview (GitHub Actions)

### Trigger Configuration
Triggered on:
- `push` to `main` branch, excluding changes in `manifest/`.
- Deploy only when code updates are committed.

### Environment Variables
Securely provided via GitHub Secrets:
- `PERSONAL_ACCESS_TOKEN`, `DOCKERHUB_USERNAME`, `DOCKERHUB_PASSWORD`
- `ARGOCD_SERVER`, `ARGOCD_USERNAME`, `ARGOCD_PASSWORD`
- `GIT_USERNAME`, `GIT_EMAIL`

## 🚀 CI/CD Workflow Overview

The GitHub Actions workflow is triggered on **pushes to the `main` branch**, excluding changes to the `manifest/` directory.

### 🔐 Secrets Used
Sensitive credentials are managed securely through GitHub Secrets, including:
- DockerHub credentials
- GitHub PAT for repository access
- ArgoCD server and login credentials
- Git configuration values


## ✅ Workflow Stages

### 1. **Build Stage**
**Job Name:** `build`  
**Purpose:** Build and publish the Docker image

#### Steps:
- **Checkout Code:** Clone the repository using a personal access token.
- **Node Setup & Install:** Set up Node.js (v14) and install dependencies via `npm`.
- **Docker Build:** Containerize the application using the current commit SHA as the tag.
- **Security Scan:** Scan the built Docker image using [Trivy](https://github.com/aquasecurity/trivy).
- **Docker Login & Push:** Authenticate to Docker Hub and push the image.


### 2. **Deploy Stage**
**Job Name:** `deploy`  
**Runs After:** Successful `build` stage  
**Purpose:** Update Kubernetes manifests and trigger deployment via ArgoCD

#### Steps:
- **Checkout Code:** Pull the latest repo content for deployment changes.
- **Install CLI Tools:** Install `kubectl` and `argocd` command-line tools.
- **Login to ArgoCD:** Authenticate using stored credentials.
- **Update Manifest:** Modify the image tag in `manifest/deployment.yaml` to match the new Docker image.
- **Commit Changes:** Push the updated manifest back to the `main` branch.
- **Sync ArgoCD App:** Trigger ArgoCD to sync and deploy the updated application.


## 🔁 CI/CD Flow Summary

1. Code push to `main` starts the workflow.
2. App is built, scanned, and pushed to Docker Hub.
3. Deployment manifest is updated with the new image tag.
4. ArgoCD is instructed to deploy the new version automatically.


## 📌 Technologies Used
- **GitHub Actions** – For automation of CI/CD pipeline
- **Docker** – For containerization and image distribution
- **Trivy** – For security vulnerability scanning
- **ArgoCD** – For GitOps-based Kubernetes deployment
- **Node.js** – As application runtime
