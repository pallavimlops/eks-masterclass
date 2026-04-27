# 03 — AWS Load Balancer Controller Installation
## Install Ingress Controller Step by Step okay!

---

## What is AWS Load Balancer Controller okay?

So here when you create Ingress in K8s —
somebody needs to actually CREATE the ALB in AWS okay!
That somebody is **AWS Load Balancer Controller** okay!

Think of it like a **translator** okay!
K8s says — I need a Load Balancer okay!
Controller translates that to AWS — create ALB okay!
Without controller — Ingress ADDRESS stays empty okay!

---

## Step 1 — Download IAM Policy okay!

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

---

## Step 2 — Create IAM Policy okay!

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

---

## Step 3 — Create OIDC Provider okay!

```bash
eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster demo-1 \
  --approve
```

---

## Step 4 — Create Service Account okay!

```bash
eksctl create iamserviceaccount \
  --cluster demo-1 \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve \
  --region us-east-1
```

Replace `<account-id>` with your AWS account ID okay!

---

## Step 5 — Get VPC ID okay!

```bash
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text
```

Copy the VPC ID okay! Need it in next step okay!

---

## Step 6 — Add Helm Repo okay!

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

---

## Step 7 — Install Controller okay!

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>
```

Replace `<your-vpc-id>` with VPC ID from Step 5 okay!

---

## Step 8 — Verify Controller is Running okay!

```bash
kubectl get pods -n kube-system | grep aws-load-balancer
```

Expected output okay!
```
aws-load-balancer-controller-xxx   1/1   Running   0
aws-load-balancer-controller-xxx   1/1   Running   0
```

---

## Step 9 — Verify Ingress gets ALB URL okay!

```bash
kubectl get ingress
```

Expected output okay!
```
NAME                  CLASS    HOSTS   ADDRESS                                        PORTS
telecom-app-ingress   <none>   *       xxxx.us-east-1.elb.amazonaws.com               80
```

ADDRESS should have ALB URL okay!
If ADDRESS is empty — controller is not working okay!

---

## Uninstall Controller okay!

```bash
helm uninstall aws-load-balancer-controller -n kube-system
```

---

## Reinstall Controller okay!

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

## Common Issues okay!

```
Issue 1 — ADDRESS is empty okay!
→ Controller not installed okay!
→ Follow steps above okay!

Issue 2 — CrashLoopBackOff okay!
→ VPC ID not passed okay!
→ Reinstall with --set vpcId okay!

Issue 3 — repo eks not found okay!
→ helm repo add eks https://aws.github.io/eks-charts
→ helm repo update okay!
```
