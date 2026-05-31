# Movie Picture Pipeline

A fully automated CI/CD pipeline for a Movie Picture catalog web application using GitHub Actions, Docker, AWS ECR, and Kubernetes (EKS).

## Project Overview

This project automates the testing, building, and deployment of a Movie Picture catalog application consisting of:

- **Frontend**: A React (TypeScript) UI that displays a list of movies
- **Backend**: A Python Flask API that serves movie data

## Architecture

GitHub Actions → Docker Build → AWS ECR → Kubernetes (EKS)

## CI/CD Pipelines

### Frontend Continuous Integration (frontend-ci.yaml)
- Trigger: Pull requests against main branch (changes in starter/frontend/) + manual dispatch
- Jobs:
  - Lint (parallel): Runs ESLint to check code quality
  - Test (parallel): Runs React Testing Library tests
  - Build: Builds Docker image (runs only after lint and test pass)

### Frontend Continuous Deployment (frontend-cd.yaml)
- Trigger: Push to main branch (changes in starter/frontend/) + manual dispatch
- Jobs:
  - Lint → Test → Build (push to ECR with git SHA tag) → Deploy to EKS

### Backend Continuous Integration (backend-ci.yaml)
- Trigger: Pull requests against main branch (changes in starter/backend/) + manual dispatch
- Jobs:
  - Lint (parallel): Runs Flake8 linter
  - Test (parallel): Runs Pytest tests
  - Build: Builds Docker image (runs only after lint and test pass)

### Backend Continuous Deployment (backend-cd.yaml)
- Trigger: Push to main branch (changes in starter/backend/) + manual dispatch
- Jobs:
  - Lint → Test → Build (push to ECR with git SHA tag) → Deploy to EKS

## Tech Stack

- GitHub Actions - CI/CD pipeline automation
- Docker - Application containerization
- AWS ECR - Docker image registry
- AWS EKS - Kubernetes cluster hosting
- Terraform - Infrastructure as Code
- Kustomize - Kubernetes manifest management
- React - Frontend framework
- Flask - Backend framework
- ESLint - Frontend linting
- Flake8 - Backend linting
- Jest - Frontend testing
- Pytest - Backend testing

## Project Structure

.github/workflows/
  frontend-ci.yaml
  frontend-cd.yaml
  backend-ci.yaml
  backend-cd.yaml
setup/
  terraform/
    main.tf
    outputs.tf
    variables.tf
    versions.tf
  init.sh
starter/
  frontend/
    k8s/
    src/
    Dockerfile
    package.json
  backend/
    k8s/
    movies/
    Dockerfile
    Pipfile
    test_app.py
screenshots/
README.md

## Setup Instructions

### Prerequisites
- AWS account with appropriate permissions
- GitHub repository
- Terraform v1.3.9+

### Step 1: Create AWS Infrastructure
cd setup/terraform
terraform init
terraform apply

### Step 2: Configure Kubernetes Access
aws eks update-kubeconfig --name cluster --region us-east-1
cd setup
./init.sh

### Step 3: Generate AWS Access Keys
1. Go to AWS Console → IAM → Users → github-action-user
2. Create access key → Application running outside AWS

### Step 4: Configure GitHub Secrets

Secrets:
- AWS_ACCESS_KEY_ID: IAM user access key
- AWS_SECRET_ACCESS_KEY: IAM user secret key

Variables:
- REACT_APP_MOVIE_API_URL: Backend LoadBalancer URL

### Step 5: Deploy
1. Run Backend CD workflow first
2. Get backend URL: kubectl get svc backend
3. Update REACT_APP_MOVIE_API_URL with the backend URL
4. Run Frontend CD workflow
5. Get frontend URL: kubectl get svc frontend
6. Open frontend URL in browser to verify

## Deployment Verification

### Backend API
curl http://<BACKEND-EXTERNAL-IP>/movies
Returns: {"movies":[{"id":"123","title":"Top Gun: Maverick"},{"id":"456","title":"Sonic the Hedgehog"},{"id":"789","title":"A Quiet Place"}]}

### Frontend
Open http://<FRONTEND-EXTERNAL-IP> in browser to see the Movie List.

## Screenshots

- frontend-app.png: Frontend displaying movie list
- backend-api.png: Backend returning movies JSON
- kubectl-get-svc.png: Kubernetes services
- kubectl-get-pods.png: Kubernetes pods
- kubectl-get-deployments.png: Kubernetes deployments
- frontend-ci.png: Frontend CI pipeline passing
- backend-ci.png: Backend CI pipeline passing
- frontend-cd.png: Frontend CD pipeline passing
- backend-cd.png: Backend CD pipeline passing
- ecr-repos.png: ECR repositories with images

## Cleanup

After project review is complete, destroy AWS resources:
cd setup/terraform
terraform destroy
