# 05 — Cleanup
## Delete Everything After Practice okay!

---

## IMPORTANT okay!

```
Always delete after practice okay!
EKS charges $0.10 per hour okay!
EC2 t3.medium charges per hour okay!
NAT Gateway charges per hour okay!
Load Balancer charges per hour okay!

Delete immediately = No charges okay!
```

---

## Step 1 — Delete K8s Resources okay!

```bash
cd /c/eks-teaching/telecom-app/k8s

kubectl delete -f ingress.yaml
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
```

---

## Step 2 — Delete Load Balancer Controller okay!

```bash
helm uninstall aws-load-balancer-controller -n kube-system
```

---

## Step 3 — Delete Node Group okay!

```bash
aws eks delete-nodegroup \
  --cluster-name demo-1 \
  --nodegroup-name demo-nodes \
  --region us-east-1
```

Wait 5 minutes okay!

---

## Step 4 — Delete Cluster okay!

```bash
eksctl delete cluster --name demo-1 --region us-east-1
```

Wait 10-15 minutes okay!

---

## Step 5 — Delete ECR Repository okay!

```bash
aws ecr delete-repository \
  --repository-name telecom-app \
  --region us-east-1 \
  --force
```

---

## Step 6 — Delete IAM Policy okay!

```bash
aws iam delete-policy \
  --policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy
```

---

## Step 7 — Delete Node IAM Role okay!

```bash
# Detach policies first okay!
aws iam detach-role-policy \
  --role-name eks-node-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy

aws iam detach-role-policy \
  --role-name eks-node-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly

aws iam detach-role-policy \
  --role-name eks-node-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy

# Delete role okay!
aws iam delete-role --role-name eks-node-role
```

---

## Step 8 — Verify Everything Deleted okay!

```bash
# No clusters okay!
aws eks list-clusters --region us-east-1

# No load balancers okay!
aws elbv2 describe-load-balancers --region us-east-1

# No EC2 instances okay!
aws ec2 describe-instances \
  --region us-east-1 \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name]" \
  --output table

# No ECR repos okay!
aws ecr describe-repositories --region us-east-1
```

All should return empty okay!

---

## Sneaky charges to check okay!

```
These keep charging even when not used okay!
Always check and delete okay!

AWS Console → EC2 → Load Balancers    → delete all okay!
AWS Console → EC2 → Target Groups     → delete all okay!
AWS Console → VPC → NAT Gateways      → delete all okay!
AWS Console → EC2 → Elastic IPs       → release all okay!
AWS Console → CloudWatch → Log Groups → delete /aws/eks/* okay!
```
