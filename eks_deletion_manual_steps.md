# EKS Cluster Deletion Guide

This document outlines the manual steps required to safely delete EKS clusters, including both default and Karpenter-enabled clusters.

---

## 📌 Steps Summary

1. Rancher, Vault, and NSK profile setup.
2. PVC and LoadBalancer service deletion.
3. Additional step for Karpenter clusters NodePools and NodeClaims deletion.
5. Run Terraform `plan` to identify known issues.
6. Known issues/errors correction and re-verify with plan.
7. Run Terraform `destroy` to delete the cluster.

---

## ⚙️ Environment Setup

### Rancher Setup

```bash
export RANCHER_CLUSTER=https://rancher.nonprod.k8s.aws.nsscloud.net/v3
export RANCHER_TOKEN_KEY=xyz
```

### NSK Setup

```bash
export NSK_PROFILE=aws-nonprod
```

### Vault Setup

This step opens the Vault webpage for Okta authentication.

```bash
export VAULT_ADDR=https://us.nonprod.kms.nskope.net
export VAULT_NAMESPACE=nonprod
export VAULT_TOKEN=$(vault login -method=oidc role=k8s-admin -format=json 2>/dev/null | jq -r .auth.client_token)
```

### Verify Variables, out put must not be empty

```bash
echo $RANCHER_CLUSTER
echo $NSK_PROFILE
echo $VAULT_ADDR
echo $VAULT_TOKEN
```

---

## ✅ Pre-check

* Always verify with NSK that the correct cluster access is being configured, Double ensure by looking Node name on Rancher/EKS UI. 

---

## 🧹 Cleanup for EKS Default Clusters (Non-Karpenter)

### List PVCs and LoadBalancer Services

```bash
kubectl get pvc --all-namespaces
kubectl get svc --all-namespaces -o wide | grep LoadBalancer
```

### Delete PVC

```bash
kubectl -n {pv_namespace} delete pvc {pv_name}
```

If stuck:

```bash
kubectl -n <NAMESPACE> patch pvc <PVC_NAME> -p '{"metadata":{"finalizers":null}}' --type=merge
```

### Delete LoadBalancer Service

```bash
kubectl -n {lb_svc_namespace} delete svc {lb_svc_name}
```

If stuck:

```bash
kubectl -n <NAMESPACE> patch svc <LB_SVC_NAME> -p '{"metadata":{"finalizers":null}}' --type=merge
```

### Re-verify

```bash
kubectl get pvc --all-namespaces
kubectl get svc --all-namespaces -o wide | grep LoadBalancer
```

➡️ Skip Karpenter steps if not applicable and proceed to Terraform.

---

## ⚡ Additional Cleanup for Karpenter Clusters

### List NodePools

```bash
kubectl get nodepools --all
```

### Delete NodePools

```bash
kubectl delete nodepools --all
```

If stuck:

```bash
kubectl patch nodepool <nodepool-name> -p '{"metadata":{"finalizers":null}}' --type=merge
```

### List NodeClaims

```bash
kubectl get nodeclaims --all
```

### Delete NodeClaims

```bash
kubectl delete nodeclaims --all
```

If stuck:

```bash
kubectl patch nodeclaim <nodeclaim-name> -p '{"metadata":{"finalizers":null}}' --type=merge
```

### Re-verify Cleanup

```bash
kubectl get nodes -l karpenter.sh/provisioner-name
kubectl get svc --all-namespaces -o wide | grep LoadBalancer
```

> ⚠️ Ensure all resources are deleted before proceeding to Terraform.

---

## 🏗️ Terraform Steps

### Clone Repository

```bash
git clone git@github.com:netSkopePlatformEng/terraform-aws-eks.git
cd terraform-aws-eks.git
```

### Run Plan

```bash
./bin/eks-deploy.sh plan clusters/comm-nonprod/<cluster_name>
```

---

## ⚠️ Known Issue: ENI Config Error

If you encounter ENI-related errors during `plan`, remove the problematic state:

```bash
./bin/eks-deploy.sh state "clusters/$EN/$CLUSTER_NAME" rm 'kubectl_manifest.eni_config["'$subnet'"]'
```

### Example

```bash
./bin/eks-deploy.sh state clusters/comm-nonprod/eks-sys-devg rm 'kubectl_manifest.eni_config["xyz-11"]'
```

Re-run `plan` and ensure all errors are resolved.

---

## 💣 Destroy Cluster

```bash
./bin/eks-deploy.sh destroy clusters/"$EN"/"$CLUSTER_NAME"
```

### Example

```bash
./bin/eks-deploy.sh destroy clusters/comm-nonprod/eks-sys-devg
```

---

## 🏁 End

Ensure all resources are fully cleaned up and Terraform completes without errors.
