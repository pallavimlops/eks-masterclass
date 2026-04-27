# 02 — Cluster Setup
## Create EKS Cluster Step by Step okay!

---

# PART 1 — CREATE EKS CLUSTER okay!

---

## Step 1 — Create Cluster okay!

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

Wait 10-15 minutes okay!

### Instance type recommendation okay!
```
t2.micro  → ❌ Too small for EKS okay!
t3.small  → ⚠️  Tight but works okay!
t3.medium → ✅ Recommended okay!
```

---

## Step 2 — Connect kubectl to Cluster okay!

```bash
aws eks update-kubeconfig \
  --region us-east-1 \
  --name demo-1
```

### Verify connection okay!
```bash
kubectl get nodes
```

Expected output okay!
```
NAME                            STATUS   ROLES    AGE
ip-xxx-xxx-xxx-xxx.ec2.internal  Ready    <none>   5m
```

---

## Step 3 — Verify Addons okay!

```bash
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

Should show these 4 okay!
```
✅ coredns
✅ eks-pod-identity-agent
✅ kube-proxy
✅ vpc-cni
```

---

# PART 2 — CREATE ECR REPOSITORY okay!

```bash
aws ecr create-repository \
  --repository-name telecom-app \
  --region us-east-1
```

---

# PART 3 — BUILD AND PUSH DOCKER IMAGE okay!

```bash
# Go to app folder okay!
cd /c/eks-teaching/telecom-app

# Get your account ID okay!
aws sts get-caller-identity --query Account --output text

# Build image okay!
docker build -t telecom-app .

# Login to ECR okay!
aws ecr get-login-password --region us-east-1 | docker login \
  --username AWS \
  --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Tag image okay!
docker tag telecom-app:latest \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v1

# Push image okay!
docker push \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v1
```

Replace `<account-id>` with your AWS account ID okay!

---

# PART 4 — INSTALL AWS LOAD BALANCER CONTROLLER okay!

This is needed to create ALB from Ingress okay!

```bash
# Step 1 — Download IAM policy okay!
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json

# Step 2 — Create IAM policy okay!
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json

# Step 3 — Create OIDC provider okay!
eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster demo-1 \
  --approve

# Step 4 — Create service account okay!
eksctl create iamserviceaccount \
  --cluster demo-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region us-east-1

# Step 5 — Get VPC ID okay!
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text

# Step 6 — Install controller okay!
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>

# Step 7 — Verify controller is running okay!
kubectl get pods -n kube-system | grep aws-load-balancer
```

Replace `<account-id>` and `<your-vpc-id>` okay!

---

# PART 5 — UPDATE DEPLOYMENT.YAML okay!

Open `telecom-app/k8s/deployment.yaml` okay!
Replace image line with your ECR URL okay!

```yaml
image: <account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v1
```

---

# PART 6 — DEPLOY TELECOM APP okay!

```bash
cd /c/eks-teaching/telecom-app/k8s

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

---

# PART 7 — GET ALB URL okay!

```bash
kubectl get ingress
```

Copy ADDRESS and open in browser okay!

```
http://<alb-url>/health
```

---

# COMPLETE FLOW okay!

```
Install tools (01-prerequisites.md)
        ↓
Create EKS cluster
        ↓
Create ECR repository
        ↓
Build and push Docker image
        ↓
Install AWS Load Balancer Controller
        ↓
Deploy telecom app (deployment, service, ingress)
        ↓
Get ALB URL
        ↓
Test in browser okay!
```
