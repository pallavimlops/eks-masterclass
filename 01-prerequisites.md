# 01 — Prerequisites
## Tools to Install Before Starting

---

## What you need

```
1. AWS Account
2. AWS CLI
3. kubectl
4. eksctl
5. Helm
6. Docker Desktop
7. Git Bash or Terminal
```

---

## Tool 1 — AWS CLI

AWS CLI is used to talk to AWS from terminal.
Think of it like a remote control for AWS.

### Install on Windows
Download from here:
```
https://awscli.amazonaws.com/AWSCLIV2.msi
```

### Verify
```bash
aws --version
```

Expected output:
```
aws-cli/2.x.x Python/3.x.x Windows/10
```

### Configure AWS CLI
```bash
aws configure
```

Enter these:
```
AWS Access Key ID     → your access key
AWS Secret Access Key → your secret key
Default region name   → us-east-1
Default output format → json
```

### How to get Access Keys
```
AWS Console
  → IAM
  → Users
  → Your user
  → Security credentials
  → Create access key
```

---

## Tool 2 — kubectl

kubectl is used to talk to Kubernetes cluster.
Think of it like a remote control for K8s.

### Install on Windows
```bash
winget install Kubernetes.kubectl
```

Or download directly:
```
https://dl.k8s.io/release/v1.29.0/bin/windows/amd64/kubectl.exe
```
Copy kubectl.exe to C:\Windows\System32

### Verify
```bash
kubectl version --client
```

---

## Tool 3 — eksctl

eksctl is used to create and manage EKS clusters.

### Install on Windows
```bash
winget install eksctl
```

Or download from here:
```
https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_windows_amd64.zip
```
Extract and copy eksctl.exe to C:\Windows\System32

### Verify
```bash
eksctl version
```

---

## Tool 4 — Helm

Helm is a package manager for Kubernetes.
Think of it like pip for Python or npm for Node.
We use it to install AWS Load Balancer Controller.

### Install on Windows
```bash
winget install Helm.Helm
```

Or download from here:
```
https://github.com/helm/helm/releases
→ Download helm-v3.x.x-windows-amd64.zip
→ Extract
→ Copy helm.exe to C:\Windows\System32
```

### Verify
```bash
helm version
```

---

## Tool 5 — Docker Desktop

Docker Desktop is used to build Docker images.

### Download from here
```
https://www.docker.com/products/docker-desktop/
```

### Verify
```bash
docker --version
```


## IAM User Permissions

Your IAM user needs these permissions:

```
✅ AmazonEKSClusterPolicy
✅ AmazonEKSWorkerNodePolicy
✅ AmazonEC2ContainerRegistryFullAccess
✅ IAMFullAccess
✅ AmazonVPCFullAccess
✅ AmazonEC2FullAccess
```

Or attach **AdministratorAccess** for learning.

---

## Node IAM Role — 3 Required Policies

When creating node group — create IAM role with these 3 policies:

```
✅ AmazonEKSWorkerNodePolicy
✅ AmazonEC2ContainerRegistryReadOnly
✅ AmazonEKS_CNI_Policy
```

### How to create Node IAM Role
```
AWS Console
  → IAM
  → Roles
  → Create Role
  → AWS Service
  → EC2          ← select EC2, NOT EKS
  → Next
  → Attach 3 policies above
  → Role name → eks-node-role
  → Create
```

---

## Quick Verification — All tools

Run all these and make sure no errors:

```bash
aws --version
kubectl version --client
eksctl version
helm version
docker --version
```

All showing versions = ready to start!
