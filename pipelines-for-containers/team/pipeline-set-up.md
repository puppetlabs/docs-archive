---
layout: default
title: "Set up a multi-stage pipeline"
---

This workflow describes creating a pipeline with three stages: development, stage, and production.

With a multi-stage pipeline you can set containers, deployments, jobs, and Helm charts up to automatically build and deploy when Pipelines detects changes to their source code. You can set up staged areas for testing, and set up automatic promotion from one stage to another.

Kubernetes uses namespaces to divide cluster resources, so each stage in your pipeline should correspond with a different namespace on your cluster. After you've created namespaces for each stage in your pipeline, and deployed to those namespaces, you can add your source code and deployments to your pipeline to automate them on build events.

Before you set up your multi-stage pipeline, make sure you’re familiar with the workflow for creating a deployment with Pipelines for Containers. For more information, see [Getting started with Pipelines for Containers](./gs_overview.html). If you're adding a Helm chart, see [Automate a Helm chart in a pipeline](./helm-pipeline.html).

## Prerequisites

Before you create the multi-stage pipeline:
* [Create a Pipelines project](./project.html) or identify an existing one to use.
* [Create a separate namespace](./cluster-namespace.html) on your cluster for each stage in your pipeline. For example, *wordpress-dev*, *wordpress-stage*, and *wordpress-prod*.
* [Build the containers and services](./container.html) you need for a working deployment, add them to your project, and deploy each of them to the namespaces that you created for your cluster.

## Create a pipeline with deployment stages  

1. Create a pipeline:
   1. In Pipelines, open your project. 
   1. Click **Pipelines**.
   1. Click **Create Pipeline** and enter a descriptive name, for example _WordPress pipeline_.
   1. Click **Add Containers** and select your container and branch. 
   1. Click **Save Source**. 
   1. Repeat steps 4 and 5 until you’ve added all of the containers you need for your deployment.
   1. Click **Close**. 
1. Add deployment stages:
   1. Click **Add Deployment Stage** and use the following settings:
      * **Cluster**: Select your cluster.
      * **Namespace**: Select your *dev* namespace.
      * **Deployment**: Select your *dev* deployment.
   1. Ensure that the correct containers are showing under **Project Containers** and click **Close**.
   1. Click **Add Deployment Stage** and use the following settings:
      * **Cluster**: Select your cluster.
      * **Namespace**: Select your stage namespace.
      * **Deployment**: Select your stage deployment.
   1. Ensure that the correct containers are showing under **Project Containers** and click **Close**.
   1. Click **Add Deployment Stage** and use the following settings:
      * **Cluster**: Select your cluster.
      * **Namespace**: Select your prod namespace.
      * **Deployment**: Select your production deployment.
   1. Ensure that the correct containers are showing under **Project Containers** and click **Close**.
1. Use auto-deploy to automate your pipeline:
   1. Turn on **Auto Build** on each of your containers: 
      1. Select **Auto Build** and then select one or more auto-build conditions to apply. 
      1. Click **X**.
   1. Select **Auto Promote on Image Event** between your build phase and *dev*.
   1. Select **Auto Promote on Image Event** between *dev* and *stage*.

You now have a working multi-stage pipeline. Each time you build a container that is linked to your pipeline, or commit to its GitHub repo, the deployments in *dev* and *stage* automatically deploy with the new build. When you’re finished testing your deployment, you can click **Promote** to manually deploy to your *prod* stage.

**Related Links**:
* For more information about the Pipelines for Containers workflow, see [Getting Started with Pipelines for Containers](./gs_overview.html). 
* For information about Kubernetes jobs in Pipelines for Containers, see [Kubernetes jobs](./job.html). 
* For information about configuring build and deploy notifications, see [Kubernetes notifications](./notification.html). 
