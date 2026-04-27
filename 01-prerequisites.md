# 01 — Prerequisites
## Tools to install before starting okay!

---

## What you need okay!

```
1. AWS Account okay!
2. AWS CLI okay!
3. kubectl okay!
4. eksctl okay!
5. Helm okay!
6. Docker Desktop okay!
7. Git Bash or Terminal okay!
```

---

## Tool 1 — AWS CLI okay!

AWS CLI is used to talk to AWS from terminal okay!
Think of it like a remote control for AWS okay!

### Install on Windows okay!
Download from here okay!
```
https://awscli.amazonaws.com/AWSCLIV2.msi
```

### Verify okay!
```bash
aws --version
```

Expected output okay!
```
aws-cli/2.x.x Python/3.x.x Windows/10
```

### Configure AWS CLI okay!
```bash
aws configure
```

Enter these okay!
```
AWS Access Key ID     → your access key okay!
AWS Secret Access Key → your secret key okay!
Default region name   → us-east-1
Default output format → json
```

### How to get Access Keys okay!
```
AWS Console
  → IAM
  → Users
  → Your user
  → Security credentials
  → Create access key okay!
```

---

## Tool 2 — kubectl okay!

kubectl is used to talk to Kubernetes cluster okay!
Think of it like a remote control for K8s okay!

### Install on Windows okay!
```bash
# Using winget okay!
winget install Kubernetes.kubectl
```

Or download directly okay!
```
https://dl.k8s.io/release/v1.29.0/bin/windows/amd64/kubectl.exe
```
Copy kubectl.exe to C:\Windows\System32 okay!

### Verify okay!
```bash
kubectl version --client
```

---

## Tool 3 — eksctl okay!

eksctl is used to create and manage EKS clusters okay!
Think of it like a remote control for EKS okay!

### Install on Windows okay!
```bash
# Using winget okay!
winget install eksctl
```

Or download from here okay!
```
https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_windows_amd64.zip
```
Extract and copy eksctl.exe to C:\Windows\System32 okay!

### Verify okay!
```bash
eksctl version
```

---

## Tool 4 — Helm okay!

Helm is a package manager for Kubernetes okay!
Think of it like pip for Python or npm for Node okay!
We use it to install AWS Load Balancer Controller okay!

### Install on Windows okay!
```bash
winget install Helm.Helm
```

Or download from here okay!
```
https://github.com/helm/helm/releases
→ Download helm-v3.x.x-windows-amd64.zip
→ Extract
→ Copy helm.exe to C:\Windows\System32
```

### Verify okay!
```bash
helm version
```

---

## Tool 5 — Docker Desktop okay!

Docker Desktop is used to build Docker images okay!

### Download from here okay!
```
https://www.docker.com/products/docker-desktop/
```

### Verify okay!
```bash
docker --version
```

### Important settings okay!
```
Open Docker Desktop
→ Settings → Resources
→ CPU    → 2
→ Memory → 2 GB
→ Apply and Restart okay!
```

---

## IAM User Permissions okay!

Your IAM user needs these permissions okay!

```
✅ AmazonEKSClusterPolicy
✅ AmazonEKSWorkerNodePolicy
✅ AmazonEC2ContainerRegistryFullAccess
✅ IAMFullAccess
✅ AmazonVPCFullAccess
✅ AmazonEC2FullAccess
```

Or attach **AdministratorAccess** for learning okay!

---

## Node IAM Role — 3 Policies okay!

When creating node group — create IAM role with these 3 policies okay!

```
✅ AmazonEKSWorkerNodePolicy
✅ AmazonEC2ContainerRegistryReadOnly
✅ AmazonEKS_CNI_Policy
```

### How to create Node IAM Role okay!
```
AWS Console
  → IAM
  → Roles
  → Create Role
  → AWS Service
  → EC2          ← select EC2 okay! NOT EKS okay!
  → Next
  → Attach 3 policies above okay!
  → Role name → eks-node-role
  → Create okay!
```

---

## Quick Verification — All tools okay!

Run all these and make sure no errors okay!

```bash
aws --version
kubectl version --client
eksctl version
helm version
docker --version
```

All showing versions = ready to start okay!
