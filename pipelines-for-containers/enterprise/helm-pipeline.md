---
layout: default
title: "Automate a Helm chart in a pipeline"
---

Automate your Helm chart by adding it to a pipeline. First, add a chart definition to the pipeline’s build stage. Next, add stages with chart deployments. Finally, select the stages that you want to automate.   

Before you begin, make sure you’re familiar with the workflow behind [creating a pipeline](./pipeline-set-up.html).

## Prerequisites

Before you add a chart to your pipeline:
* [Deploy your custom Helm Chart](./helm-chart-custom.html) to the namespaces you plan to automate in your pipeline.
* [Create a pipeline](./pipeline-set-up.html) or identify one to use.
* [Add the containers](./pipeline-set-up.html) that are packaged in your Helm chart to your pipeline. This gives you increased control over the containers in your chart.

To automate your pipeline:
1. Select your project.
1. Click **Pipelines** and select your pipeline.
1. Add a chart build stage:
   1. Click **Add Helm Chart Build Stage**.
   1. Select a Helm chart definition and a branch, and click **Add Helm Chart Stage**. Your chart is listed under **Sources**, and appears in your Pipeline as a blank field. This field updates each time you build your chart.
   1. To build the chart, push a change to its source repository and increment the version number in `Chart.yaml`.
> **Important**: Pipelines disregards any changes you've made if you don’t increment the chart version in the `Chart.yaml` file.
1. Map a chart deployment to a stage in your pipeline:
   1. Click **Add Chart Deployment Stage** if you’re adding a new stage for your chart, or **Add Chart Deployment** if you’re adding your chart to an existing stage.
   1. Select a chart deployment.
   1. Select a container image to map to your deployment.
   1. Select a chart key to map to the name of your container. For example, `image: mysql`.
   1. Select a chart key to map to your container tag. For example, `imageTag: 5.7.14`.
> **Important**: If your chart combines the container’s name and tag in a single YAML entry -- for example, `mysql:5.7.14` -- check the option to Use same value for tag.  
   1. (Optional) Click **Add container** to add more containers to your deployment.
   1. Click **Add Chart Deployment**.
1. Automate the chart:
   1. Between the build stage and the first deployment stage in your pipeline, check the option to **Auto Promote on Image Event**. Each time you commit to one of your sources, your pipeline deploys your changes to the first stage in your pipeline.
   1. Check the option to **Auto Promote** between the deployment stages in your pipeline that you want to automate.
1. (Optional) If you prefer not to automate a specific stage in your environment -- for example, a production stage -- or you need to roll back a chart, use manual promotion: 
   1. Click **Promote**.
   1. Select your chart.
   1. (Optional) If available, select a deployment to promote.
   1. (Optional) If available, select a job to promote. 
   1. Click **Promote**.
> **Important**: After you’ve added a chart deployment to a pipeline, you can deploy it only by promoting it in your pipeline. A Helm chart deployment that you’ve added to a pipeline won’t be accessible on the Helm charts page.

Each time you commit a change to a chart that you’ve added to the pipeline, pipelines deploys to the stages that you’ve chosen to automate.

**Related links**:
* For more information about the fundamental Pipelines for Containers workflow, see [Getting started with Pipelines for Containers](./gs_overview.html).  
* For more information about jobs in Pipelines, see [Add a job to your pipeline](./pipeline-add-job.html). 
* For information about configuring build and deploy notifications, see [Kubernetes notifications](./notification.html).
