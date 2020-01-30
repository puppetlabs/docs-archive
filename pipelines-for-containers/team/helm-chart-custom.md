---
layout: default
title: "Build and deploy a custom Helm Chart"
--- 

Use the Pipelines for Containers web UI to build and deploy a custom Helm Chart.

Before you begin, make sure you've [added a Kubernetes cluster](./cluster-add.html) to Pipelines for Containers, installed <a href="https://github.com/helm/helm/blob/master/docs/install.md" target="_blank">Tiller</a> inside the cluster, and created a namespace in the cluster for your Helm Chart.

> **Note**: If you'd prefer to deploy a pre-packaged application from one of the many public Helm Chart repositories available online, see [Deploy a Helm Chart from a public repository](./helm-chart.html). Alternatively, you can fork the repo at <a href="https://github.com/helm/charts/tree/master/stable" target="_blank">https://github.com/helm/charts/tree/master/stable</a> and follow the steps below using a pre-built chart. 

1. In Pipelines, select your project.
1. Click **Helm Charts**.
1. Connect your Tiller instance:
   1. Click **Connect Tiller Instance**.
   1. Click **Select Cluster** and select your cluster. 
   1. Click **Select Namespace** and select the namespace where you installed Tiller.
      > **Important**: If you installed Tiller by running `helm init`, select the `kube-system` namespace.   
   1. Click **Connect Tiller**.
1. Add the chart definition:
   1. In Pipelines, select your project.
   1. Click **Helm Charts**.
   1. Click **Add Chart Definition**.
   1. Click **Select Source Control**, and select your source control. 
   1. Click **Select Source Repository** and select your source repository.
   1. Enter a descriptive name for your chart.  
      > **Note**: Helm does not use this field to assign a name to the chart: a chart's name is defined in its Chart.yaml. Use this field to help you identify your chart in Pipelines for Containers.  
   1. Click **Chart.yaml** and enter the path to `Chart.yaml` in your repo, for example `stable/mysql/Chart.yaml`.
   1. Click **Add Chart**. 
1. Build the chart definition:
   1. Click **Build Chart Definition**.
   1. Select the branch for your chart definition, for example `master`.
   1. Click **Build** and wait for the build to complete. 
1. Deploy the chart:
   1. Click **Deploy** next to the chart you want to deploy.
   1. Click **Select Cluster** and select your cluster. 
   1. Click **Select Tiller** and select your Tiller. 
   1. Click **Select Namespace** and select the namespace you created for your Helm Chart. 
   1. Click **Select Chart Version** and select the chart that you built above.
   1. Click **Deploy** and wait for the chart to deploy.

**Related Links**:
* For more information about using public Helm Chart repos, see [Deploy a Helm Chart from a public repository](./helm-chart.html).
* For more information about setting up an automated pipeline, see [Set up a multi-stage Pipeline](./pipeline-set-up.html).
* For information on adding a cluster to your Pipelines project, see [Adding a Kubernetes cluster](./cluster-add.html). 
