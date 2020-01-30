---
layout: default
title: "Deploy a Helm Chart from a public repository"
---

## Deploy a Helm Chart from a public repository

Use the Pipelines for Containers web UI to deploy a pre-packaged application from one of the many public Helm Chart repositories available online. 

> **Before you begin** make sure you have [added a Kubernetes cluster](./cluster-add.html) to Pipelines for Containers, installed [Tiller](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-tiller) inside the cluster, and [created a namespace in the cluster](./cluster-namespace.html) for your Helm Chart. 

1. In the Pipelines for Containers web UI, click **Projects** and select a project from your list. 
2. Click **Helm Charts**. The Helm Charts overview pane opens. 
3. Click **Connect Helm Chart Repository** and follow the prompts to connect your chosen public Helm Chart repository. 
   > **Tip:** Connect to the primary public Helm Chart repository by entering `stable` as the repository name and `https://kubernetes-charts.storage.googleapis.com/` as the URL.
4. Click **Connect Tiller Instance** and follow the prompts to connect your Tiller instance. 
   > **Important:** If you installed Tiller by running `helm init`, select the `kube-system` namespace when you connect your Tiller instance to Pipelines for Containers.
5. Locate the Helm Chart you wish to deploy. Click **View Charts**, locate your chosen Helm Chart in the list, and click **Deploy** (rocketship icon).
6. On the deployment screen, follow the prompts to select your cluster, Tiller instance, namespace, and chart version. After you've selected these variables, review the default chart values and override any defaults as required. When you're done making changes, click **Deploy**. 
   > **Tip:** Refer to the documentation for your chosen Helm Chart to learn about alternate chart values. For example, if deploying the mySQL Helm Chart, you might wish to adjust the persistence and memory settings.
7. Review the results of your deployment on the deployment status page. After the deployment is complete, follow the notes provided by Tiller to take your next steps with your newly deployed application. 
   > **Important:** Depending on the size of the containers involved in your deployment and other variables such as connection speed, it may take up to several minutes for your deployment to complete. 
   
## Update a previously deployed Helm Chart

You can make changes to the settings or chart version of a previously deployed Helm Chart before redeploying.  

1. In the Pipelines for Containers web UI, click **Projects** and select a project from your list. 
2. Click **Helm Charts**. The Helm Charts overview pane opens. 
3. In the **Deployed Charts** list, locate the Helm Chart you wish to update and click **Deploy** (rocketship icon). 
4. On the deployment screen, update the chart version, if desired. Review the chart values and make any changes. 
   > **Important:** Pipelines for Containers preserves your custom chart values. If you previously deployed a Helm Chart with customized values, and then change to a new version of the Helm Chart, the chart values field are not overwritten with the defaults for the new version. As a result, if the new chart version includes values that must be specified, you must add them to the existing list manually. Refer to your Helm Chart's documentation to learn about any new values. 
5. When you're done making changes, click **Deploy**. 
6. Review the results of your deployment on the deployment status page. After the deployment is complete, follow the notes provided by Tiller to take your next steps with your newly deployed application. 
   
## Delete a deployed Helm Chart 

After you deploy a Helm chart, the chart remains in your master list on the Helm charts overview page, where you can re-deploy them. If you decide that a given Helm Chart is no longer needed, delete it from the list. 

> **Caution:** Deleting a deployed Helm Chart also deletes the Kubernetes resources in your cluster associated with that Helm Chart. 

1. In the Pipelines for Containers web UI, click **Projects** and select a project from your list. 
2. Click **Helm Charts**. The Helm Charts overview pane opens. 
3. In the **Deployed Charts** list, locate the Helm Chart you wish to delete and click the delete icon. 
4. Review the progress of your deletion on the deployment status page. 


