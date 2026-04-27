## 02 — Cluster Setup
## Create EKS Cluster Step by Step

---

# PART 1 — CREATE EKS CLUSTER

## Step 1 — Create Cluster

```bash
eksctl create cluster \
  --name demo-1 \
  --region us-east-1 \
  --nodegroup-name demo-nodes \
  --node-type t3.medium \
  --nodes 1 \
  --nodes-min 1 \
  --nodes-max 1
```

Wait 10-15 minutes.

### Instance type recommendation
```
t2.micro  → ❌ Too small for EKS
t3.small  → ⚠️  Tight but works

---

## Step 2 — Connect kubectl to Cluster

```bash
aws eks update-kubeconfig \
  --region us-east-1 \
  --name demo-1
```

### Verify connection
```bash
kubectl get nodes
```

Expected output:
```
NAME                            STATUS   ROLES    AGE
ip-xxx-xxx-xxx-xxx.ec2.internal  Ready    <none>   5m
```

---

## Step 3 — Verify Addons

```bash
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

Should show these 4:
```
✅ coredns
✅ eks-pod-identity-agent
✅ kube-proxy
✅ vpc-cni
```

---

# PART 2 — CREATE ECR REPOSITORY

```bash
aws ecr create-repository \
  --repository-name <your-app-name> \
  --region us-east-1
```

---

# PART 3 — BUILD AND PUSH DOCKER IMAGE

```bash
# Get your account ID
aws sts get-caller-identity --query Account --output text

# Build image
docker build -t <your-app-name> .

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login \
  --username AWS \
  --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Tag image
docker tag <your-app-name>:latest \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/<your-app-name>:v1

# Push image
docker push \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/<your-app-name>:v1
```

Replace `<account-id>` with your AWS account ID.

---

# PART 4 — DEPLOY APPLICATION

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

---

# PART 5 — GET ALB URL

```bash
kubectl get ingress
```

Copy ADDRESS and open in browser:
```
http://<alb-url>/health
```

---

# COMPLETE FLOW

```
Install tools (01-prerequisites.md)
        ↓
Create EKS cluster
        ↓
Create ECR repository
        ↓
Build and push Docker image
        ↓
Install AWS Load Balancer Controller (03-ingress-controller.md)
        ↓
Deploy application (deployment, service, ingress)
        ↓
Get ALB URL and test in browser
```
