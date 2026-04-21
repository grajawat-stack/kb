# EKS Cluster Deletion Guide

This document outlines the manual steps required to safely delete EKS clusters, including both default and Karpenter-enabled clusters.

---

## 📌 Steps Summary

1. Rancher, Vault, and NSK profile setup  
2. PVC / LoadBalancer service deletion (eks-default clusters)  
3. PVC / LoadBalancer / NodePools / NodeClaims deletion (Karpenter clusters)  
4. Run Terraform `plan` to identify known issues  
5. Run Terraform `destroy` to delete the cluster  

---

## ⚙️ Environment Setup

### Rancher Setup
```bash
export RANCHER_CLUSTER=https://rancher.nonprod.k8s.aws.nsscloud.net/v3
export RANCHER_TOKEN_KEY=xyz

### NSK Setup
```bash
export NSK_PROFILE=aws-nonprod

### Vault Setup
```bash
export VAULT_ADDR=https://us.nonprod.kms.nskope.net
export VAULT_NAMESPACE=nonprod
export VAULT_TOKEN=$(vault login -method=oidc role=k8s-admin -format=json 2>/dev/null | jq -r .auth.client_token)


✅ Pre-check
Always verify with NSK that the correct cluster access is configured.
🧹 Cleanup for EKS Default Clusters (Non-Karpenter)
List PVCs and LoadBalancer Services

```bash
kubectl get pvc --all-namespaces
kubectl get svc --all-namespaces -o wide | grep LoadBalancer


kubectl -n {pv_namespace} delete pvc {pv_name}
