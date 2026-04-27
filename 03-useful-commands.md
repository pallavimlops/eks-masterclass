# 03 — Useful Commands
## All commands you need for EKS okay!

---

# CLUSTER COMMANDS okay!

```bash
# List all clusters okay!
aws eks list-clusters --region us-east-1

# Connect kubectl to cluster okay!
aws eks update-kubeconfig --region us-east-1 --name demo-1

# Describe cluster okay!
aws eks describe-cluster --name demo-1 --region us-east-1

# Get VPC ID of cluster okay!
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text

# List addons okay!
aws eks list-addons --cluster-name demo-1 --region us-east-1
```

---

# NODE COMMANDS okay!

```bash
# Get all nodes okay!
kubectl get nodes

# Get nodes with more details okay!
kubectl get nodes -o wide

# Describe a node okay!
kubectl describe node <node-name>

# Check node resource usage okay!
kubectl top nodes
```

---

# POD COMMANDS okay!

```bash
# Get all pods in default namespace okay!
kubectl get pods

# Get all pods in all namespaces okay!
kubectl get pods -A

# Get pods with more details okay!
kubectl get pods -o wide

# Describe a pod okay!
kubectl describe pod <pod-name>

# Get pod logs okay!
kubectl logs <pod-name>

# Get previous pod logs okay!
kubectl logs <pod-name> --previous

# Get logs and follow okay!
kubectl logs <pod-name> -f

# Execute command inside pod okay!
kubectl exec -it <pod-name> -- /bin/bash

# Check pod resource usage okay!
kubectl top pods

# Watch pods in real time okay!
kubectl get pods --watch
```

---

# DEPLOYMENT COMMANDS okay!

```bash
# Get all deployments okay!
kubectl get deployments

# Describe deployment okay!
kubectl describe deployment <deployment-name>

# Scale deployment okay!
kubectl scale deployment telecom-app --replicas=3

# Update image okay!
kubectl set image deployment/telecom-app \
  telecom-app=<account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v2

# Rollout status okay!
kubectl rollout status deployment/telecom-app

# Rollback deployment okay!
kubectl rollout undo deployment/telecom-app

# See rollout history okay!
kubectl rollout history deployment/telecom-app
```

---

# SERVICE COMMANDS okay!

```bash
# Get all services okay!
kubectl get svc

# Get services with more details okay!
kubectl get svc -o wide

# Describe service okay!
kubectl describe svc <service-name>
```

---

# INGRESS COMMANDS okay!

```bash
# Get all ingress okay!
kubectl get ingress

# Describe ingress okay!
kubectl describe ingress <ingress-name>
```

---

# NAMESPACE COMMANDS okay!

```bash
# Get all namespaces okay!
kubectl get namespaces

# Create namespace okay!
kubectl create namespace my-namespace

# Get pods in specific namespace okay!
kubectl get pods -n kube-system

# Get all resources in namespace okay!
kubectl get all -n kube-system
```

---

# APPLY AND DELETE COMMANDS okay!

```bash
# Apply yaml file okay!
kubectl apply -f deployment.yaml

# Apply all files in folder okay!
kubectl apply -f k8s/

# Delete using yaml file okay!
kubectl delete -f deployment.yaml

# Delete all files in folder okay!
kubectl delete -f k8s/

# Delete specific pod okay!
kubectl delete pod <pod-name>

# Delete specific deployment okay!
kubectl delete deployment <deployment-name>
```

---

# DEBUGGING COMMANDS okay!

```bash
# Check events okay!
kubectl get events --sort-by='.lastTimestamp'

# Check events in all namespaces okay!
kubectl get events -A --sort-by='.lastTimestamp'

# Check configmap okay!
kubectl describe configmap aws-auth -n kube-system

# Check all resources okay!
kubectl get all

# Check all resources in all namespaces okay!
kubectl get all -A
```

---

# ECR COMMANDS okay!

```bash
# Create repository okay!
aws ecr create-repository \
  --repository-name telecom-app \
  --region us-east-1

# List repositories okay!
aws ecr describe-repositories --region us-east-1

# List images in repository okay!
aws ecr list-images \
  --repository-name telecom-app \
  --region us-east-1

# Login to ECR okay!
aws ecr get-login-password --region us-east-1 | docker login \
  --username AWS \
  --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Delete repository okay!
aws ecr delete-repository \
  --repository-name telecom-app \
  --region us-east-1 \
  --force
```

---

# DOCKER COMMANDS okay!

```bash
# Build image okay!
docker build -t telecom-app .

# List images okay!
docker images

# Tag image okay!
docker tag telecom-app:latest \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v1

# Push image okay!
docker push \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/telecom-app:v1

# Run container locally okay!
docker run -p 5000:5000 telecom-app

# List running containers okay!
docker ps

# Stop container okay!
docker stop <container-id>
```

---

# HELM COMMANDS okay!

```bash
# Add repo okay!
helm repo add eks https://aws.github.io/eks-charts

# Update repos okay!
helm repo update

# List installed charts okay!
helm list -A

# Install chart okay!
helm install <name> <chart>

# Uninstall chart okay!
helm uninstall <name> -n <namespace>
```

---

# IAM COMMANDS okay!

```bash
# Get your account ID okay!
aws sts get-caller-identity --query Account --output text

# List roles okay!
aws iam list-roles \
  --query "Roles[*].RoleName" \
  --output table

# List attached policies for role okay!
aws iam list-attached-role-policies --role-name <role-name>

# Detach policy from role okay!
aws iam detach-role-policy \
  --role-name <role-name> \
  --policy-arn <policy-arn>

# Delete role okay!
aws iam delete-role --role-name <role-name>
```

---

# EC2 COMMANDS okay!

```bash
# List running instances okay!
aws ec2 describe-instances \
  --region us-east-1 \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name,PrivateDnsName]" \
  --output table

# Terminate instance okay!
aws ec2 terminate-instances \
  --instance-ids <instance-id> \
  --region us-east-1

# List load balancers okay!
aws elbv2 describe-load-balancers --region us-east-1
```

---

# QUICK REFERENCE okay!

```bash
kubectl get nodes              → check nodes okay!
kubectl get pods               → check pods okay!
kubectl get pods -A            → all pods okay!
kubectl get svc                → check services okay!
kubectl get ingress            → check ingress okay!
kubectl describe pod <name>    → debug pod okay!
kubectl logs <pod-name>        → pod logs okay!
kubectl top nodes              → node resource usage okay!
kubectl top pods               → pod resource usage okay!
kubectl get events             → cluster events okay!
```
