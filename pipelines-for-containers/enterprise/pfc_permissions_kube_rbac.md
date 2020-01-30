---
layout: default
title: "Connect to an RBAC-enabled Kubernetes cluster"
--- 

Use a service account to enable a connection from Pipelines for Containers to a Kubernetes cluster with role-based access control (RBAC) enabled. 

Kubernetes introduced RBAC in version 1.6. It allows you to create permissions that apply to your entire cluster, or to namespaces within your cluster. You can create Kubernetes service accounts to define those permissions in your cluster and then add the cluster to your Pipelines account, where you can use Pipelines for Containers RBAC (hereafter called Pipelines RBAC) to assign permissions to groups of users. The owner or administrator of your Pipelines account should add and authenticate the cluster to take advantage of Pipelines RBAC. 

To connect to an RBAC-enabled cluster, generate a Kubernetes service account and create a cluster role with cluster-wide permission to list all namespaces. Use a cluster role binding to bind the cluster role to the service account. This is the only cluster-wide permission you need to give the account. Next, create a role for each namespace on the cluster. The role you create gives much wider access at the namespace level. Use role bindings to bind each role to the service account. This grants Pipelines permission to perform operations on each namespace on the cluster. Finally, add your cluster to Pipelines and authenticate it with your Kubernetes service account token.

Before you begin, make sure you’ve installed the <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/" target="_blank">Kubernetes command-line interface.</a>

> **Important:** Legacy authentication must be disabled to use Kubernetes RBAC. For more on legacy authentication, see [Enable legacy authentication](./pfc_permissions_legacy_auth.html) 

To set up the service account, you need cluster administrator permissions to create:
* service accounts
* cluster roles
* cluster role bindings
* roles
* role bindings

To set up a service account:
1. Give yourself permission to create roles by creating a cluster role binding and binding it to your user account. Your user account is usually the email address associated with your cloud provider account.

   ```
   kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin   --user <YOUR_USER_ACCOUNT>
   ```

1. Generate a service account on the default namespace:

   ```
   kubectl create serviceaccount <YOUR_SERVICE_ACCOUNT> -n default
   ```
   
   This generates the following service account:
   
   ```
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     creationTimestamp: 2018-09-05T22:01:31Z
     name: <YOUR_SERVICE_ACCOUNT>
     namespace: default
     resourceVersion: "133965"
     selfLink: /api/v1/namespaces/default/serviceaccounts/<YOUR_SERVICE_ACCOUNT>
     uid: 3f5c9c08-b157-11e8-9d59-42010a8a0135
     secrets:
     - name: <YOUR_SERVICE_ACCOUNT_SECRET>
   ```

1. Create a cluster role with permissions to list all namespaces:
   
   ```
   kubectl create clusterrole <YOUR_CLUSTER_ROLE> --verb=list --resource=namespaces
   ```
   
   This generates the following cluster role:
  
   ```
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     creationTimestamp: 2018-09-04T22:28:11Z
     name: <YOUR_CLUSTER_ROLE>
     resourceVersion: "255196"
     selfLink: /apis/rbac.authorization.k8s.io/v1/clusterroles/<YOUR_CLUSTER_ROLE>
     uid: ce6a647a-b091-11e8-9d59-42010a8a0135
   rules:
   - apiGroups:
     - ""
     resources:
     - namespaces
     verbs:
     - list
   ```
   
1. Bind your cluster role to the service account with a cluster role binding:
   
   ```
   kubectl create clusterrolebinding <YOUR_CLUSTER_ROLE_BINDING> --clusterrole=<YOUR_CLUSTER_ROLE> --serviceaccount=default:<YOUR_SERVICE_ACCOUNT>
   ```
   
   This generates the following cluster role binding:
   
   ```
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     creationTimestamp: 2018-09-05T22:08:51Z
     name: <YOUR_CLUSTER_ROLE_BINDING>
     resourceVersion: "134560"
     selfLink: /apis/rbac.authorization.k8s.io/v1/clusterrolebindings/<YOUR_CLUSTER_ROLE_BINDING>
     uid: 45b5b270-b158-11e8-9d59-42010a8a0135
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: <YOUR_CLUSTER_ROLE>
   subjects:
     - kind: ServiceAccount
     name: <YOUR_SERVICE_ACCOUNT>
     namespace: default
   ```

1. Create a role for each namespace that you want Pipelines to manage. The role grants wider permissions to perform actions (verbs) and access resources on the namespace:

   ```
   kubectl create role <YOUR_NAMESPACE_ROLE> --verb=update,get,list,create,patch,delete    --resource=deployments,jobs,horizontalpodautoscalers,configmaps,pods,pods/log,pods/portforward,services,replicasets,secrets -n <YOUR_NAMESPACE>
   ```

   This generates the following role:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     creationTimestamp: 2018-09-10T19:11:26Z
     name: <YOUR_NAMESPACE_ROLE>
     namespace: <YOUR_NAMESPACE>
     resourceVersion: "830971"
     selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/<YOUR_NAMESPACE>/roles/<YOUR_NAMESPACE_ROLE>
     uid: 50a2f217-b52d-11e8-a0f0-42010a8a00fa
   rules:
   - apiGroups: ["", "extensions", "batch", "autoscaling"] 
     resources:
     - configmaps
     - deployments
     - jobs
     - horizonalpodautoscalers
     - pods
     - pods/log
     - pods/portforward
     - services
     - replicasets
     - secrets
     verbs:
     - update
     - get
     - list
     - create
     - patch
     - delete 
   ```
   
1. Bind the role to the service account with a role binding:
   
   ```
   kubectl create rolebinding <YOUR_ROLE_BINDING> --role=<YOUR_NAMESPACE_ROLE> --serviceaccount=default:<YOUR_SERVICE_ACCOUNT>
   ```

   This generates the following role binding: 

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     creationTimestamp: 2018-09-10T19:19:00Z
     name: <YOUR_ROLE_BINDING>
     namespace: default
     resourceVersion: "704110"
     selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/default/rolebindings/<YOUR_ROLE_BINDING>
     uid: 5f1f79d6-b52e-11e8-a0f0-42010a8a00fa
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: Role
     name: <YOUR_NAMESPACE_ROLE>
   subjects:
   - kind: ServiceAccount
     name: <YOUR_SERVICE_ACCOUNT>
     namespace: default
   ```
1. (Optional) Repeat steps 4 and 5 for every namespace on the cluster that you want to connect to Pipelines.


## Connect to your cluster in Pipelines for Containers

1. Generate and decode your service account token:
   1. Find your service account token by listing your secrets:
      
      ```
      kubectl get secret
      ```
      
   1. Generate your service account token:
      
      ```
      kubectl get secret <YOUR_SERVICE_ACCOUNT_SECRET> -o yaml
      ```
      
   1. Copy the token and decode it:

      ```
      echo <YOUR_SERVICE_ACCOUNT_TOKEN> | base64 --decode
      ```
      
   1. Copy the decoded token.
1. Add your cluster to Pipelines:
   1. In Pipelines, click **Clusters** > **Add Cluster** > **Add Existing Cluster**.
   1. Select **Bare Metal** > **Continue**.
   1. Enter your cluster name, region, and master endpoint (You do not need to enter a port).
> **Note**: Your cluster endpoint is the IP Address of your cluster. Use `kubectl get endpoints` to find your endpoint.
   1. Select **Connect** using cluster API token.
   1. Enter your decoded service account token from earlier.
   1. Select **Add Cluster**. 

You’ve created a service account with the necessary permissions to connect to a Kubernetes RBAC enabled cluster and access its namespaces. You can use [Groups](./group.html) to manage access to specific clusters and namespaces within Pipelines.  

Related Links:
* For more information about RBAC in Kubernetes, see <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">Using RBAC authorization</a>. 
* For more information about Pipelines RBAC, see [Groups](./group.html). 