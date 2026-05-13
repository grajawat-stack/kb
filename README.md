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



## PCM TICKET FOR sys-terraform-eks user (Accout name must the aws account name)

```bash

Request: Create a sys-terraform-eks user in the following account and add it to the Vault prod namespace.
Account ID: 299975068617
Account Name: ns-prod-pe-cd-dplymnt-orchestrator


## NET TICKET (check already created pod subnets in tracking sheet)

```bash
Require subnets in AWS network account for nodes and pods:

Environment: Production
Participant Account: 299975068617
Region: us-east-1
Cluster: eks-cdss-dcp-prod-use1
Requirements

Nodes:
3 × /26 subnets (CGN)

Pods:
3 × /21 subnets

240.22.80.0/21	
240.22.88.0/21		
240.22.96.0/21

We’re tracking EKS Clusters here:
https://github.com/netSkopePlatformEng/terraform-aws-eks/blob/master/network/pod-subnets.md

