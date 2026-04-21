# EKS Cluster Decommissioning Guide

This document outlines the mandatory manual steps for cleaning up resources and destroying EKS clusters (Standard and Karpenter-enabled) using Terraform.

---

## 1. Environment Setup

Configure your environment variables for Rancher, NSK, and Vault.

### Rancher & NSK Setup
```bash
export RANCHER_CLUSTER=[https://rancher.nonprod.k8s.aws.nsscloud.net/v3](https://rancher.nonprod.k8s.aws.nsscloud.net/v3)
export RANCHER_TOKEN_KEY=xyz
export NSK_PROFILE=aws-nonprod
