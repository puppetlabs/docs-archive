---
layout: default
title: "Setting cluster permissions"
---

Kubernetes uses role-based access control (RBAC) to regulate access to clusters. If you're setting up a new cluster, or you're receiving "permission denied" or "forbidden" errors on existing clusters, you need to give Pipelines permission to connect to your Kubernetes cluster. 

There are three ways to connect a Kubernetes cluster to your Pipelines account:

* You can [set up a Kubernetes service account](./pfc_permissions_kube_rbac.html) and then use Pipelines RBAC to grant permissions to your users. This method allows you to compartmentalize teams and users from each other at the Kubernetes permissions level, but requires you to configure multiple service accounts to grant each team access to specific namespaces. This is the recommended method.
* You can [add the client user as a cluster-admin](./pfc_permissions_add_client_user.html). This method is convenient, but it will give all of the users on your account access to all of the namespaces on your clusters. This method is good for evaluating Pipelines, or for smaller clusters where multiple users do not need to be strictly compartmentalized from each other.
* If you're using the Google Kubernetes Engine, you can [enable legacy authentication](./pfc_permissions_legacy_auth.html) on your cluster. This essentially disables RBAC. This method is not recommended.

Related topics:
* For more information about RBAC in Kubernetes, see <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/" target="_blank">Using RBAC authorization</a>. 
* For more information about Pipelines RBAC, see [Groups](./group.html). 