# kb
kb articles , my notepad
Env
---
https://netskope.atlassian.net/wiki/spaces/SYE/pages/3865182810/Argo+CD


<https://rancher.nonprod.k8s.aws.nsscloud.net>

<https://argo.nonprod.k8s.aws.nsscloud.net>

PCM ticket guide
----------------
Can you please create sys-terraform-eks user in Create sys-terraform-eks user in 930936105014 (ns-nonprod-eng-rbi-fedalpha) and put it in vault nonprod namespace.


Netticket Example
-----------------
https://netskope.atlassian.net/browse/NET-26233

AWS Prod EKS Subnet allocations for ns-prod-eng-dlp-ami 878202268893

Require subnets in production AWS network account for nodes and pods:

Regions: eu-west-2
AWS account: 878202268893

3x /26 for nodes 

3x /21 for pods 

240.22.208.0/21
240.22.216.0/21
240.22.224.0/21


# DELETE SEC 

$ nsk cluster kubeconfig --name local
$ export KUBECONFIG=~/.nsk/local.yaml
(⎈|local:default)$ kubens argo
(⎈|local:argo)$ kubectl delete $(kubectl get secret -o name | grep eks-workflow-service-dev)
secret "cluster-eks-workflow-service-dev" deleted
secret "metadata-eks-workflow-service-dev" deleted
