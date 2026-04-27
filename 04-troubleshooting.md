# 04 — Troubleshooting
## Common Errors and Fixes okay!

---

## Error 1 — kubectl cannot connect to cluster okay!

```
Error: no such host
Unable to connect to the server
```

Fix okay!
```bash
aws eks update-kubeconfig \
  --region us-east-1 \
  --name demo-1
```

---

## Error 2 — Ingress ADDRESS is empty okay!

```
NAME                  CLASS    HOSTS   ADDRESS   PORTS
telecom-app-ingress   <none>   *                 80
```

Fix okay!
AWS Load Balancer Controller is not installed okay!
Follow Part 4 in 02-cluster-setup.md okay!

---

## Error 3 — Too many Pods okay!

```
0/1 nodes are available: 1 Too many pods
```

Fix okay!
```bash
# Delete unused deployments okay!
kubectl delete deployment metrics-server -n kube-system
kubectl delete deployment external-dns -n external-dns
```

---

## Error 4 — Controller CrashLoopBackOff okay!

```
aws-load-balancer-controller   CrashLoopBackOff
```

Fix okay!
```bash
# Check logs okay!
kubectl logs -n kube-system deployment/aws-load-balancer-controller

# Reinstall with VPC ID okay!
helm uninstall aws-load-balancer-controller -n kube-system

# Get VPC ID okay!
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text

# Reinstall okay!
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=<your-vpc-id>
```

---

## Error 5 — ImagePullBackOff okay!

```
Pod status: ImagePullBackOff
```

Fix okay!
```bash
# Check image URL in deployment.yaml is correct okay!
kubectl describe pod <pod-name>

# Make sure ECR repository exists okay!
aws ecr describe-repositories --region us-east-1

# Make sure Node IAM Role has this policy okay!
# AmazonEC2ContainerRegistryReadOnly
```

---

## Error 6 — Node not joining cluster okay!

```
kubectl get nodes
No resources found
```

Fix okay!
```
Check 1 → Node IAM Role has all 3 policies okay!
  ✅ AmazonEKSWorkerNodePolicy
  ✅ AmazonEC2ContainerRegistryReadOnly
  ✅ AmazonEKS_CNI_Policy

Check 2 → Node is in PUBLIC subnet okay!
  AWS Console → EC2 → Instance → Networking tab
  Subnet route table must have Internet Gateway okay!

Check 3 → Security group allows outbound traffic okay!
```

---

## Error 7 — CrashLoopBackOff okay!

```
Pod status: CrashLoopBackOff
```

Fix okay!
```bash
# Check pod logs okay!
kubectl logs <pod-name> --previous

# Check pod events okay!
kubectl describe pod <pod-name>

# Common reasons okay!
→ App is crashing okay!
→ Wrong environment variables okay!
→ Cannot connect to database okay!
→ Out of memory okay!
```

---

## Error 8 — Pending Pods okay!

```
Pod status: Pending
```

Fix okay!
```bash
kubectl describe pod <pod-name>

# Common reasons okay!
→ Insufficient CPU or Memory → upgrade instance type okay!
→ Too many pods → delete unused pods okay!
→ Node not ready → check node status okay!
```

---

## Error 9 — AccessDenied okay!

```
Error: AccessDenied
```

Fix okay!
```bash
# Check your IAM user has enough permissions okay!
aws sts get-caller-identity

# Add AdministratorAccess to your IAM user okay!
# AWS Console → IAM → Users → Your user → Add permissions
```

---

## Error 10 — Helm repo not found okay!

```
Error: repo eks not found
```

Fix okay!
```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

---

## General Debugging Steps okay!

```bash
# Step 1 — Check pods okay!
kubectl get pods -A

# Step 2 — Check events okay!
kubectl get events --sort-by='.lastTimestamp'

# Step 3 — Check pod logs okay!
kubectl logs <pod-name>

# Step 4 — Describe pod okay!
kubectl describe pod <pod-name>

# Step 5 — Check nodes okay!
kubectl get nodes -o wide
```
