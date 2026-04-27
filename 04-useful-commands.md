# 04 — Useful Commands
## All Commands You Need for EKS

---

# CLUSTER COMMANDS

```bash
# List all clusters
aws eks list-clusters --region us-east-1

# Connect kubectl to cluster
aws eks update-kubeconfig --region us-east-1 --name demo-1

# Describe cluster
aws eks describe-cluster --name demo-1 --region us-east-1

# Get VPC ID of cluster
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text

# List addons
aws eks list-addons --cluster-name demo-1 --region us-east-1

# Get cluster version
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.version" \
  --output text
```

---

# NODE COMMANDS

```bash
# Get all nodes
kubectl get nodes

# Get nodes with more details
kubectl get nodes -o wide

# Describe a node
kubectl describe node <node-name>

# Check node resource usage
kubectl top nodes
```

---

# POD COMMANDS

```bash
# Get all pods in default namespace
kubectl get pods

# Get all pods in all namespaces
kubectl get pods -A

# Get pods with more details
kubectl get pods -o wide

# Describe a pod
kubectl describe pod <pod-name>

# Get pod logs
kubectl logs <pod-name>

# Get previous pod logs
kubectl logs <pod-name> --previous

# Follow pod logs
kubectl logs <pod-name> -f

# Execute command inside pod
kubectl exec -it <pod-name> -- /bin/bash

# Check pod resource usage
kubectl top pods

# Watch pods in real time
kubectl get pods --watch
```

---

# DEPLOYMENT COMMANDS

```bash
# Get all deployments
kubectl get deployments

# Describe deployment
kubectl describe deployment <deployment-name>

# Scale deployment
kubectl scale deployment <deployment-name> --replicas=3

# Update image
kubectl set image deployment/<deployment-name> \
  <container-name>=<account-id>.dkr.ecr.us-east-1.amazonaws.com/<app>:v2

# Rollout status
kubectl rollout status deployment/<deployment-name>

# Rollback deployment
kubectl rollout undo deployment/<deployment-name>

# See rollout history
kubectl rollout history deployment/<deployment-name>
```

---

# SERVICE COMMANDS

```bash
# Get all services
kubectl get svc

# Get services with more details
kubectl get svc -o wide

# Describe service
kubectl describe svc <service-name>
```

---

# INGRESS COMMANDS

```bash
# Get all ingress
kubectl get ingress

# Describe ingress
kubectl describe ingress <ingress-name>
```

---

# NAMESPACE COMMANDS

```bash
# Get all namespaces
kubectl get namespaces

# Create namespace
kubectl create namespace my-namespace

# Get pods in specific namespace
kubectl get pods -n kube-system

# Get all resources in namespace
kubectl get all -n kube-system
```

---

# APPLY AND DELETE COMMANDS

```bash
# Apply yaml file
kubectl apply -f deployment.yaml

# Apply all files in folder
kubectl apply -f k8s/

# Delete using yaml file
kubectl delete -f deployment.yaml

# Delete all files in folder
kubectl delete -f k8s/

# Delete specific pod
kubectl delete pod <pod-name>

# Delete specific deployment
kubectl delete deployment <deployment-name>
```

---

# DEBUGGING COMMANDS

```bash
# Check events
kubectl get events --sort-by='.lastTimestamp'

# Check events in all namespaces
kubectl get events -A --sort-by='.lastTimestamp'

# Check configmap
kubectl describe configmap aws-auth -n kube-system

# Check all resources
kubectl get all

# Check all resources in all namespaces
kubectl get all -A
```

---

# ECR COMMANDS

```bash
# Create repository
aws ecr create-repository \
  --repository-name <app-name> \
  --region us-east-1

# List repositories
aws ecr describe-repositories --region us-east-1

# List images in repository
aws ecr list-images \
  --repository-name <app-name> \
  --region us-east-1

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login \
  --username AWS \
  --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

# Delete repository
aws ecr delete-repository \
  --repository-name <app-name> \
  --region us-east-1 \
  --force
```

---

# DOCKER COMMANDS

```bash
# Build image
docker build -t <app-name> .

# List images
docker images

# Tag image
docker tag <app-name>:latest \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/<app-name>:v1

# Push image
docker push \
  <account-id>.dkr.ecr.us-east-1.amazonaws.com/<app-name>:v1

# Run container locally
docker run -p 5000:5000 <app-name>

# List running containers
docker ps

# Stop container
docker stop <container-id>
```

---

# HELM COMMANDS

```bash
# Add repo
helm repo add eks https://aws.github.io/eks-charts

# Update repos
helm repo update

# List installed charts
helm list -A

# Install chart
helm install <name> <chart>

# Uninstall chart
helm uninstall <name> -n <namespace>
```

---

# IAM COMMANDS

```bash
# Get your account ID
aws sts get-caller-identity --query Account --output text

# List roles
aws iam list-roles \
  --query "Roles[*].RoleName" \
  --output table

# List attached policies for role
aws iam list-attached-role-policies --role-name <role-name>

# Detach policy from role
aws iam detach-role-policy \
  --role-name <role-name> \
  --policy-arn <policy-arn>

# Delete role
aws iam delete-role --role-name <role-name>
```

---

# EC2 COMMANDS

```bash
# List running instances
aws ec2 describe-instances \
  --region us-east-1 \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name,PrivateDnsName]" \
  --output table

# Terminate instance
aws ec2 terminate-instances \
  --instance-ids <instance-id> \
  --region us-east-1

# List load balancers
aws elbv2 describe-load-balancers --region us-east-1
```

---

# QUICK REFERENCE

```bash
kubectl get nodes              → check nodes
kubectl get pods               → check pods
kubectl get pods -A            → all pods
kubectl get svc                → check services
kubectl get ingress            → check ingress and ALB URL
kubectl describe pod <name>    → debug pod
kubectl logs <pod-name>        → pod logs
kubectl top nodes              → node resource usage
kubectl top pods               → pod resource usage
kubectl get events             → cluster events
```
