# 06 — EKS Cluster Upgrade
## Step by Step Upgrade Guide okay!

---

## Why Upgrade okay?

```
AWS supports only last 3-4 K8s versions okay!
Older versions lose support okay!
Security patches stop okay!
New features not available okay!

So upgrade regularly okay!
```

---

## Golden Rule of EKS Upgrade okay!

```
Always upgrade in this order okay!

Step 1 → Upgrade Control Plane first okay!
Step 2 → Upgrade Addons okay!
Step 3 → Upgrade Node Groups okay!

Never skip steps okay!
Never upgrade more than 1 minor version at a time okay!

Example okay!
1.27 → 1.28 → 1.29 → 1.30
Not 1.27 → 1.30 directly okay!
```

---

## Before Upgrade — Checklist okay!

```
✅ Check current version okay!
✅ Read AWS release notes for new version okay!
✅ Take backup of important data okay!
✅ Test in dev/staging first okay!
✅ Plan for maintenance window okay!
✅ Inform team okay!
```

---

## Step 1 — Check Current Version okay!

```bash
# Check cluster version okay!
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.version" \
  --output text

# Check node version okay!
kubectl get nodes

# Check addon versions okay!
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Step 2 — Check Available Versions okay!

```bash
aws eks describe-addon-versions \
  --region us-east-1 \
  --query "addons[*].addonVersions[*].addonVersion" \
  --output table
```

---

## Step 3 — Upgrade Control Plane okay!

```bash
aws eks update-cluster-version \
  --name demo-1 \
  --kubernetes-version 1.29 \
  --region us-east-1
```

Wait 10-15 minutes okay!

### Check upgrade status okay!
```bash
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.status" \
  --output text
```

Wait until status shows **ACTIVE** okay!

---

## Step 4 — Upgrade Addons okay!

Upgrade each addon one by one okay!

### Upgrade VPC CNI okay!
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name vpc-cni \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade CoreDNS okay!
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name coredns \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade Kube Proxy okay!
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name kube-proxy \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade Pod Identity Agent okay!
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name eks-pod-identity-agent \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Check addon status okay!
```bash
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Step 5 — Upgrade Node Group okay!

```bash
aws eks update-nodegroup-version \
  --cluster-name demo-1 \
  --nodegroup-name demo-nodes \
  --region us-east-1
```

Wait 10-15 minutes okay!

### Check node group status okay!
```bash
aws eks describe-nodegroup \
  --cluster-name demo-1 \
  --nodegroup-name demo-nodes \
  --region us-east-1 \
  --query "nodegroup.status" \
  --output text
```

Wait until status shows **ACTIVE** okay!

### Verify nodes are upgraded okay!
```bash
kubectl get nodes
```

Version should show new K8s version okay!

---

## Step 6 — Verify Everything okay!

```bash
# Check cluster version okay!
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.version" \
  --output text

# Check nodes okay!
kubectl get nodes

# Check all pods are running okay!
kubectl get pods -A

# Check addons okay!
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Upgrade Summary okay!

```
Current Version → Target Version
     1.27       →     1.28

Step 1 → Control Plane 1.27 → 1.28 okay!
         Wait for ACTIVE status okay!

Step 2 → Addons upgrade okay!
         vpc-cni okay!
         coredns okay!
         kube-proxy okay!
         eks-pod-identity-agent okay!

Step 3 → Node Group 1.27 → 1.28 okay!
         Wait for ACTIVE status okay!

Step 4 → Verify all okay!
```

---

## Important Notes okay!

```
✅ Always upgrade control plane first okay!
✅ Never skip minor versions okay!
✅ Upgrade addons after control plane okay!
✅ Upgrade nodes last okay!
✅ Test application after each step okay!
✅ Keep old node group as backup okay!
✅ Upgrade during low traffic time okay!
```
