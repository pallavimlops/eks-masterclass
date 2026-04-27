# 03 — AWS Load Balancer Controller Installation
## Install Ingress Controller Step by Step

---

## What is AWS Load Balancer Controller?

When you create Ingress in K8s —
somebody needs to actually CREATE the ALB in AWS.
That somebody is **AWS Load Balancer Controller**.

Without controller — Ingress ADDRESS stays empty.

```
Ingress created
      ↓
AWS Load Balancer Controller sees it
      ↓
Goes to AWS and creates ALB automatically
      ↓
ALB is connected to your K8s service
```

---

## Step 1 — Download IAM Policy

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

---

## Step 2 — Create IAM Policy

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

---

## Step 3 — Create OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster demo-1 \
  --approve
```

---

## Step 4 — Create Service Account

```bash
eksctl create iamserviceaccount \
  --cluster demo-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region us-east-1
```

Replace `<account-id>` with your AWS account ID.

---

## Step 5 — Get VPC ID

```bash
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text
```

Copy the VPC ID. Need it in next step.

---

## Step 6 — Add Helm Repo

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

---

## Step 7 — Install Controller

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>
```

Replace `<your-vpc-id>` with VPC ID from Step 5.

---

## Step 8 — Verify Controller is Running

```bash
kubectl get pods -n kube-system | grep aws-load-balancer
```

Expected output:
```
aws-load-balancer-controller-xxx   1/1   Running   0
aws-load-balancer-controller-xxx   1/1   Running   0
```

---

## Step 9 — Verify Ingress gets ALB URL

```bash
kubectl get ingress
```

Expected output:
```
NAME                  CLASS    HOSTS   ADDRESS                              PORTS
telecom-app-ingress   <none>   *       xxxx.us-east-1.elb.amazonaws.com     80
```

ADDRESS should have ALB URL.
If ADDRESS is empty — controller is not working.

---

## Uninstall Controller

```bash
helm uninstall aws-load-balancer-controller -n kube-system
```

---

## Reinstall Controller

```bash
helm uninstall aws-load-balancer-controller -n kube-system

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>
```

---

## Common Issues

```
Issue 1 — ADDRESS is empty
→ Controller not installed
→ Follow steps above

Issue 2 — CrashLoopBackOff
→ VPC ID not passed
→ Reinstall with --set vpcId

Issue 3 — repo eks not found
→ helm repo add eks https://aws.github.io/eks-charts
→ helm repo update
```
