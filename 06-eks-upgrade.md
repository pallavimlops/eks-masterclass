# 06 — EKS Cluster Upgrade
## Step by Step Upgrade Guide

---

## Why Upgrade?

```
AWS supports only last 3-4 K8s versions
Older versions lose support
Security patches stop
New features not available

Upgrade regularly!
```

---

## Golden Rule of EKS Upgrade

```
Always upgrade in this order:

Step 1 → Upgrade Control Plane first
Step 2 → Upgrade Addons
Step 3 → Upgrade Node Groups

Never skip steps!
Never upgrade more than 1 minor version at a time!

Example:
1.27 → 1.28 → 1.29 → 1.30
Not 1.27 → 1.30 directly!
```

---

## Before Upgrade — Checklist

```
✅ Check current version
✅ Read AWS release notes for new version
✅ Take backup of important data
✅ Test in dev/staging first
✅ Plan for maintenance window
✅ Inform team
```

---

## Step 1 — Check Current Version

```bash
# Check cluster version
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.version" \
  --output text

# Check node version
kubectl get nodes

# Check addon versions
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Step 2 — Check Available Versions

```bash
aws eks describe-addon-versions \
  --region us-east-1 \
  --query "addons[*].addonVersions[*].addonVersion" \
  --output table
```

---

## Step 3 — Upgrade Control Plane

```bash
aws eks update-cluster-version \
  --name demo-1 \
  --kubernetes-version 1.29 \
  --region us-east-1
```

Wait 10-15 minutes.

### Check upgrade status
```bash
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.status" \
  --output text
```

Wait until status shows **ACTIVE**.

---

## Step 4 — Upgrade Addons

Upgrade each addon one by one.

### Upgrade VPC CNI
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name vpc-cni \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade CoreDNS
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name coredns \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade Kube Proxy
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name kube-proxy \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Upgrade Pod Identity Agent
```bash
aws eks update-addon \
  --cluster-name demo-1 \
  --addon-name eks-pod-identity-agent \
  --resolve-conflicts OVERWRITE \
  --region us-east-1
```

### Check addon status
```bash
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Step 5 — Upgrade Node Group

```bash
aws eks update-nodegroup-version \
  --cluster-name demo-1 \
  --nodegroup-name demo-nodes \
  --region us-east-1
```

Wait 10-15 minutes.

### Check node group status
```bash
aws eks describe-nodegroup \
  --cluster-name demo-1 \
  --nodegroup-name demo-nodes \
  --region us-east-1 \
  --query "nodegroup.status" \
  --output text
```

Wait until status shows **ACTIVE**.

### Verify nodes are upgraded
```bash
kubectl get nodes
```

Version should show new K8s version.

---

## Step 6 — Verify Everything

```bash
# Check cluster version
aws eks describe-cluster \
  --name demo-1 \
  --region us-east-1 \
  --query "cluster.version" \
  --output text

# Check nodes
kubectl get nodes

# Check all pods are running
kubectl get pods -A

# Check addons
aws eks list-addons \
  --cluster-name demo-1 \
  --region us-east-1
```

---

## Upgrade Summary

```
Current Version → Target Version
     1.27       →     1.28

Step 1 → Control Plane 1.27 → 1.28
         Wait for ACTIVE status

Step 2 → Upgrade Addons
         vpc-cni
         coredns
         kube-proxy
         eks-pod-identity-agent

Step 3 → Node Group 1.27 → 1.28
         Wait for ACTIVE status

Step 4 → Verify all
```

---

## Important Notes

```
✅ Always upgrade control plane first
✅ Never skip minor versions
✅ Upgrade addons after control plane
✅ Upgrade nodes last
✅ Test application after each step
✅ Keep old node group as backup
✅ Upgrade during low traffic time
```
