---
layout: default
title: "Adding clusters"
--- 

You can connect Pipelines to an existing cluster or provision a Google Kubernetes Engine (GKE) cluster.

Currently, you can use Pipelines to create a GKE cluster. Alternatively, you can connect an existing cluster from any location to Pipelines. Pipelines supports most major Kubernetes cloud providers, including Google Kubernetes Engine (GKE) and Amazon Elastic Container Service for Kubernetes (EKS).

Before you create a GKE cluster in Pipelines, or connect an existing cluster, you should decide how you want to manage access on the cluster. This decision will affect how you connect to the cluster in Pipelines. For more information, see [Set cluster permissions](./cluster-set-permissions.html). 

* [Add a Google Kubernetes Engine cluster](./pfc_add_gke_cluster.html)
* [Connect to a cluster using legacy authentication](./pfc_connect_non_gke_cluster.html)
* [Connect to an RBAC-enabled Kubernetes cluster](./pfc_permissions_kube_rbac.html)