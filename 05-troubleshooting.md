# 05 — Troubleshooting
## Common Errors and Fixes

---

## Error 1 — kubectl cannot connect to cluster

```
Error: no such host
Unable to connect to the server
```

Fix:
```bash
aws eks update-kubeconfig \
  --region us-east-1 \
  --name demo-1
```

---

## Error 2 — Ingress ADDRESS is empty

```
NAME                  CLASS    HOSTS   ADDRESS   PORTS
telecom-app-ingress   <none>   *                 80
```

Fix:
AWS Load Balancer Controller is not installed.
Follow 03-ingress-controller.md.

---

## Error 3 — Too many Pods

```
0/1 nodes are available: 1 Too many pods
```

Fix:
```bash
# Delete unused deployments
kubectl delete deployment metrics-server -n kube-system
kubectl delete deployment external-dns -n external-dns
```

---

## Error 4 — Controller CrashLoopBackOff

```
aws-load-balancer-controller   CrashLoopBackOff
```

Fix:
```bash
# Check logs
kubectl logs -n kube-system deployment/aws-load-balancer-controller

# Get VPC ID
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text

# Reinstall with VPC ID
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

## Error 5 — ImagePullBackOff

```
Pod status: ImagePullBackOff
```

Fix:
```bash
# Check image URL in deployment.yaml is correct
kubectl describe pod <pod-name>

# Make sure ECR repository exists
aws ecr describe-repositories --region us-east-1

# Make sure Node IAM Role has this policy
# AmazonEC2ContainerRegistryReadOnly
```

---

## Error 6 — Node not joining cluster

```
kubectl get nodes
No resources found
```

Fix:
```
Check 1 → Node IAM Role has all 3 policies
  ✅ AmazonEKSWorkerNodePolicy
  ✅ AmazonEC2ContainerRegistryReadOnly
  ✅ AmazonEKS_CNI_Policy

Check 2 → Node is in PUBLIC subnet
  AWS Console → EC2 → Instance → Networking tab
  Subnet route table must have Internet Gateway

Check 3 → Security group allows outbound traffic
```

---

## Error 7 — CrashLoopBackOff

```
Pod status: CrashLoopBackOff
```

Fix:
```bash
# Check pod logs
kubectl logs <pod-name> --previous

# Check pod events
kubectl describe pod <pod-name>

# Common reasons
→ App is crashing
→ Wrong environment variables
→ Cannot connect to database
→ Out of memory
```

---

## Error 8 — Pending Pods

```
Pod status: Pending
```

Fix:
```bash
kubectl describe pod <pod-name>

# Common reasons
→ Insufficient CPU or Memory → upgrade instance type
→ Too many pods → delete unused pods
→ Node not ready → check node status
```

---

## Error 9 — AccessDenied

```
Error: AccessDenied
```

Fix:
```bash
# Check your IAM user has enough permissions
aws sts get-caller-identity

# Add AdministratorAccess to your IAM user
# AWS Console → IAM → Users → Your user → Add permissions
```

---

## Error 10 — Helm repo not found

```
Error: repo eks not found
```

Fix:
```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

---

## General Debugging Steps

```bash
# Step 1 — Check pods
kubectl get pods -A

# Step 2 — Check events
kubectl get events --sort-by='.lastTimestamp'

# Step 3 — Check pod logs
kubectl logs <pod-name>

# Step 4 — Describe pod
kubectl describe pod <pod-name>

# Step 5 — Check nodes
kubectl get nodes -o wide
```
