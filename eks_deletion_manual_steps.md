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

### Rancher, NSK, Vault Setup
```bash
export RANCHER_CLUSTER=https://rancher.nonprod.k8s.aws.nsscloud.net/v3
export RANCHER_TOKEN_KEY=xyz
export NSK_PROFILE=aws-nonprod
export VAULT_ADDR=https://us.nonprod.kms.nskope.net
export VAULT_NAMESPACE=nonprod
export VAULT_TOKEN=$(vault login -method=oidc role=k8s-admin -format=json 2>/dev/null | jq -r .auth.client_token)


#✅ Pre-check
#Always verify with NSK that the correct cluster access is configured.
#🧹 Cleanup for EKS Default Clusters (Non-Karpenter)
# List PVCs and LoadBalancer Services

kubectl get pvc --all-namespaces
kubectl get svc --all-namespaces -o wide | grep LoadBalancer


kubectl -n {pv_namespace} delete pvc {pv_name}




⚡ Additional Cleanup for Karpenter Clusters
List NodePools
kubectl get nodepools --all
Delete NodePools
kubectl delete nodepools --all

If stuck:
kubectl patch nodepool <nodepool-name> -p '{"metadata":{"finalizers":null}}' --type=merge


List NodeClaims
kubectl get nodeclaims --all
Delete NodeClaims
kubectl delete nodeclaims --all

If stuck:
kubectl patch nodeclaim <nodeclaim-name> -p '{"metadata":{"finalizers":null}}' --type=merge
Re-verify Cleanup
kubectl get nodes -l karpenter.sh/provisioner-name
kubectl get svc --all-namespaces -o wide | grep LoadBalancer

⚠️ Ensure all resources are deleted before proceeding to Terraform.


🏗️ Terraform Steps

Clone Repository

git clone git@github.com:netSkopePlatformEng/terraform-aws-eks.git
cd terraform-aws-eks.git

Run Plan
./bin/eks-deploy.sh plan clusters/comm-nonprod/<cluster_name>
⚠️ Known Issue: ENI Config Error

If you encounter ENI-related errors during plan, remove the problematic state:

./bin/eks-deploy.sh state "clusters/$EN/$CLUSTER_NAME" rm 'kubectl_manifest.eni_config["'$subnet'"]'
Example
./bin/eks-deploy.sh state clusters/comm-nonprod/eks-sys-devg rm 'kubectl_manifest.eni_config["xyz-11"]'
Re-run plan and ensure all errors are resolved.



💣 Destroy Cluster
./bin/eks-deploy.sh destroy clusters/"$EN"/"$CLUSTER_NAME"

./bin/eks-deploy.sh destroy clusters/comm-nonprod/eks-sys-devg
