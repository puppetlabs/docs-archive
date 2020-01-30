---
layout: default
title: "Enable legacy authentication"
---

Enable legacy authentication if you want to connect your Google Kubernetes Engine (GKE) cluster to Pipelines for Containers without using role-based access control (RBAC). 

> **Important**: The recommended method for connecting to an RBAC-enabled cluster is to [create a service account](./pfc_permissions_kube_rbac.html) and then use Pipelines RBAC to grant permissions to your users.    

To enable legacy authentication:
Open the Google Cloud Platform dashboard at https://console.cloud.google.com/.
On the navigation menu, click **Kubernetes Engine**.
Select your cluster and click **Edit**.
Under **Legacy Authorization**, select **Enabled**.
Click **Save** and wait for the cluster to update.

You can now access your cluster without receiving "forbidden" or "permission denied" errors.

Related Links:
For more information about Pipelines RBAC, see [Groups](./group.html). 
