# Kubernetes-Troubleshooting
# ImagePullBackOff due to Wrong Docker Image Name or Tag

## Scenario Overview
This document demonstrates how an `ImagePullBackOff` error occurs in Kubernetes
when an incorrect Docker image name or tag is used, and how to fix it.

This scenario is reproduced on a **local Minikube cluster**.

---

## Prerequisites
- Minikube installed and running
- kubectl configured
- Internet access to Docker Hub

---

## Step 1: Create Deployment with WRONG Image Name Deployment given in repo just change image name wrongly 


## Troubleshooting Kubernetes Deployment

### Step 1: Apply the Deployment
```
kubectl apply -f deployment.yaml

```

### Step 2: Observe the Issue
Check pod status:

```
kubectl get pods
```

Expected output:

```
ImagePullBackOff
```

### Step 3: Check Pod Events

```
kubectl describe pod <pod-name>

```
You will see:

```
Failed to pull image 
repository does not exist or may require 'docker login'
```

## Why This Happens

- Kubernetes does not validate image names
- Docker Hub cannot find the image or tag  
- Image pull fails repeatedly, resulting in **ImagePullBackOff**

## Fix: Correct the Image Name or Tag

Update the Deployment image:
```yaml
image: inginx:1.14.2

```
Always verify the exact image name and tag on Docker Hub.

## Delete Pod

```
kubectl delete pod <pod-name>

```

Expected Result

```
kubectl get pods
```

Output:
```
Running
```

Key Learnings
Even a small typo in image name causes ImagePullBackOff

Always use explicit image tags

ImagePullBackOff is not always an authentication issue


