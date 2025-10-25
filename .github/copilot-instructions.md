# Copilot Instructions for Kubernetes CI/CD Tutorial

## Project Overview
This is a **tutorial project** demonstrating Kubernetes CI/CD patterns with a simple Node.js grade service API. The architecture follows GitOps principles with ArgoCD for continuous deployment.

### Key Components
- **Grade Service API** (`src/app.js`): Simple Express.js REST API with in-memory storage
- **Docker Container** (`Dockerfile`): Node.js 14-based containerization 
- **GitHub Actions CI/CD** (`.github/workflows/ci-cd.yml`): Builds images and updates GitOps repo
- **GitOps Repository**: Separate repo (`grade-api-gitops`) contains Kubernetes manifests

## Critical Workflows

### Local Development
```bash
cd src/
npm install
node app.js  # Runs on port 3000
```

### CI/CD Pipeline Triggers
- **Push to main**: Builds Docker image, pushes to GitHub Container Registry (ghcr.io), updates GitOps repo
- **Pull requests**: Builds and tests (no deployment)
- Uses **self-hosted runners** (not GitHub-hosted)

### Container Registry Pattern
Images are tagged with both `latest` and commit SHA:
```
ghcr.io/shoshoavi/kubernetes-cicd-tutorial:latest
ghcr.io/shoshoavi/kubernetes-cicd-tutorial:abc123...
```

## Project-Specific Conventions

### GitOps Update Pattern
The CI/CD pipeline automatically updates a **separate GitOps repository** (`rslim087a/grade-api-gitops`) by:
1. Cloning the GitOps repo during CI
2. Using `sed` to update the image tag in `deployment.yaml`
3. Cross-platform sed handling (macOS vs Linux differences)

### API Structure
The grade service follows a simple REST pattern:
- `GET /grades`: Returns all grades array
- `POST /grades`: Adds grade with auto-generated ID (timestamp-based)
- Grade object: `{ id, name, subject, score }`

### Tutorial Context
This codebase is designed for **learning purposes**. When making changes:
- Keep the API simple and educational
- Maintain compatibility with the tutorial documentation
- The comment in `app.js` ending with "Trigger CI/CD pipeline..." is intentional for demo purposes

## Dependencies & External Integrations

### Required Secrets
- `CR_PAT`: GitHub Container Registry access token
- `GITOPS_PAT`: Token for updating GitOps repository

### ArgoCD Integration
The project assumes ArgoCD is installed in the target cluster and monitors the GitOps repo. See README.md for ArgoCD setup commands.

### Docker Build Context
Dockerfile expects the Node.js app in `src/` directory, copies package files first for layer caching optimization.

## Key Files to Understand
- `src/app.js`: Core application logic and API endpoints
- `.github/workflows/ci-cd.yml`: Complete CI/CD automation including GitOps updates
- `Dockerfile`: Container build strategy
- `README.md`: Tutorial prerequisites and ArgoCD setup instructions