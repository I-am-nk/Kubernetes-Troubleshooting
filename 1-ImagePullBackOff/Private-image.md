# ImagePullBackOff due to Private Docker Repository

## Scenario Overview
This document explains how an `ImagePullBackOff` error occurs when using a
**private Docker Hub repository** without authentication and how to fix it
using Kubernetes `imagePullSecrets`.

This scenario is tested on **Minikube**.

---

## Prerequisites
- Minikube running
- kubectl configured
- Private Docker Hub repository
- Docker Hub Personal Access Token (PAT)

---

## Step 1: Make Docker Repository Private

- Go to Docker Hub
- Open your repository
- Settings → Change visibility to **Private**

## Step 2: Create Deployment WITHOUT imagePullSecrets

## Apply Deployment

```bash
kubectl apply -f deployment.yaml
```
## Step 3: Observe the Error

```
kubectl get pods
kubectl describe pod <pod-name>
```

Error message:

```
pull access denied for username/image_name:tag
unauthorized: authentication required
```

Why This Happens
- Private Docker repositories require authentication
- Kubernetes pulls images anonymously by default
- Docker Hub denies access

## Fix: Create Docker Registry Secret

## Step 1: Create Docker Hub PAT
Docker Hub → Account Settings → Security

Create a Read-only Personal Access Token

## Step 2: Create Kubernetes Secret

```
kubectl create secret docker-registry dockerhub-secret \
  --docker-username=<docker-username> \
  --docker-password=<DOCKER_HUB_PAT> \
  --docker-email=<YOUR_EMAIL>
```

Verify:

```
kubectl get secrets

```
## Step 3: Update Deployment to Use Secret

```
spec:
  imagePullSecrets:
  - name: dockerhub-secret
  containers:
    - name: nginx
      image: nginx:1.14.2

```
⚠️ imagePullSecrets must be under spec.template.spec.

## Step 4: Restart Deployment

```
kubectl rollout restart deployment private-repo-demo
```
Expected Result

```
kubectl get pods
```
Output:

```
Running
```

## Key Learnings
- Private registries always need imagePullSecrets
- Secrets are namespace-scoped
- Docker PATs are recommended over passwords