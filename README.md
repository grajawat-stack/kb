# kb

## Links
- Argo CD Docs: https://netskope.atlassian.net/wiki/spaces/SYE/pages/3865182810/Argo+CD  
- Rancher (Non-Prod): https://rancher.nonprod.k8s.aws.nsscloud.net  
- Argo (Non-Prod): https://argo.nonprod.k8s.aws.nsscloud.net  

---

## PCM Ticket Guide

**Request:**
Create a `sys-terraform-eks` user in the following account and add it to the Vault nonprod namespace.

- **Account ID:** 930936105014  
- **Account Name:** ns-nonprod-eng-rbi-fedalpha  

---

## Netticket Example

- Ticket: https://netskope.atlassian.net/browse/NET-26233  

### AWS Prod EKS Subnet Allocations

- **Environment:** Production  
- **Account:** 878202268893  
- **Region:** eu-west-2  
- **Cluster:** ns-prod-eng-dlp-ami  

### Requirements

- **Nodes:**  
  - 3 × /26 subnets  

- **Pods:**  
  - 3 × /21 subnets  
    - 240.22.208.0/21  
    - 240.22.216.0/21  
    - 240.22.224.0/21  

---

## Delete Secrets

```bash
nsk cluster kubeconfig --name local
export KUBECONFIG=~/.nsk/local.yaml
kubens argo
kubectl delete $(kubectl get secret -o name | grep eks-workflow-service-dev)
