---
layout: default
title: "Add the client user as a cluster administrator"
--- 

If you want to add an RBAC-enabled Kubernetes cluster to your account and you would prefer not to use a service account, you can add the `client` user as a cluster administrator.

> **Important**: Using this method to connect to your cluster gives all the users on your Pipelines account admin-level access to your cluster. The recommended method for connecting to an RBAC-enabled cluster is to [create a service account](./pfc_permissions_kube_rbac.html) and then use Pipelines RBAC to grant permissions to your users.    

To add client as a cluster administrator:
1. Connect to your cluster.
1. Select your cluster:

   ```
   kubectl config set-cluster <YOUR_CLUSTER_NAME>
   ```
     
1. Do one of the following:
   * Add client as a cluster-admin:
     
     ```
     kubectl create clusterrolebinding client-cluster-admin-binding --clusterrole=cluster-admin --user=client
     ```
     
   * Alternatively, add the following permission to your existing cluster-admin binding:
     
     ```
     kubectl edit clusterrolebinding cluster-admin
     ```
     
     ```
     - apiGroup: rbac.authorization.k8s.io
     kind: User
     name: client
     ```

You can now access your cluster without receiving "forbidden" or "permission denied" errors.

Related links:
* For more information about RBAC in Kubernetes, see <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/" target="_blank">Using RBAC authorization</a>. 
* For more information about Pipelines RBAC, see [Groups](./group.html). 
