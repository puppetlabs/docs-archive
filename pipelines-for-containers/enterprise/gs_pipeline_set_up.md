---
layout: default
title: "Set up a pipeline"
--- 

After you deploy WordPress, the next step is to automate your deployments and container builds in a pipeline. Containers and deployments that you’ve added to a pipeline are built and deployed automatically every time you make changes to your container repos. 

A typical pipeline has a build stage and one or more deployment stages. When you add a container to the pipeline, it becomes a pipeline source. The pipeline monitors its sources to check for updates. When a change occurs, the pipeline builds the containers in the build stage. Next, the pipeline checks the rules you've set to see if you want to promote the changes to the first deployment stage in your pipeline. If so, it updates the deployment with the new containers. 

In previous steps, you deployed WordPress to three namespaces on your cluster. The deployments in these namespaces will serve as the three deployment stages in your pipeline. 

1. Create a Pipeline and add your WordPress and MySQL containers:
   1. From your Pipelines project, click **Pipelines**.
   1. Click **Create Pipeline** and enter a descriptive name, for example *WordPress Pipeline*.
   1. Click **Add Containers** and select the following:
      * **Container:** k8s-mysql
      * **Branch:** master
      * **Regex:** Leave blank.
   1. Click **Save Source**.
   1. Click **Add New Source** and select the following:
      * **Container**: k8s-wordpress
      * **Branch**: master
      * **Regex**: Leave blank. 
   1. Click **Close**. 
      Your containers are listed under **Sources** at the top of the pipeline. The build stage below **Sources** is empty because you haven't triggered an automatic build yet.
1. Set your build triggers:
   1. In the **k8s-mysql** source, click **Auto Build**. You have three options:
      * **Commit**: The container builds every time Pipelines detects a commit to the container's repo.
      * **PullRequest**: The container builds every time Pipelines detects a merged pull request to the container's repo.
      * **Tag**: The container builds every time Pipelines detects a tagged commit to the container's repo.
   1. Select all three build options and click **X**.
   1. In the k8s-wordpress source, click **Auto Build**.
   1. Select all three build options and click **X**.
      Your container sources are now set up to build every time Pipelines detects a commit, pull request, or tag in your source repository.
1. Add deployment stages:
   1. Click **Add Deployment Stage** and select the following:
      * **Cluster**: your cluster
      * **Namespace**: wordpress-dev
      * **Deployment**: my-wordpress-deployment
   1. Make sure the names of the containers in the **Deployment Containers** column match those in the **Project Containers column**. Pipelines detects the containers in the deployment and maps them to the containers that you've added as pipeline sources. Every time you build a source container, Pipelines updates your deployments. 
   1. Click **Add Stage**.  
   1. Click **Add Deployment Stage** and select the following:
      * **Cluster**: your cluster
      * **Namespace**: wordpress-stage
      * **Deployment**: my-wordpress-deployment
   1. Make sure the names of the containers in the **Deployment Containers** column match those in the **Project Containers** column.
   1. Click **Add Stage**.
   1. Click **Add Deployment Stage** and select the following:
      * **Cluster**: your cluster
      * **Namespace**: wordpress-prod
      * **Deployment**: my-wordpress-deployment
   1. Make sure the names of the containers in the **Deployment Containers** column match those in the **Project Containers** column.
   1. Click **Add Stage**.
1. Use **Auto Promote** to automate your pipeline. 
   1. Select **Auto Promote on Image Event** between the phase and the wordpress-dev stage. When you select this option, Pipelines automatically promotes any changes from the build stage to the wordpress-dev deployment. 
   1. Select **Auto Promote** between the wordpress-dev and the wordpress-stage deployment stages to have Pipelines promote successful deployments from wordpress-dev to wordpress-stage.   
   1. Leave the final **Auto Promote** unselected between the wordpress-stage and wordpress-prod stages. Disabling automatic promotion to the final stage in your pipeline lets you check that everything is working as expected before you promote your changes to your production stage.
1. Test your pipeline by triggering a build:
   1. Create a file called `README.md` inside your `k8s-wordpress` directory.
   1. Enter a short description of your WordPress Docker container and save the file.
   1. Commit the file to your repo:
      
      ```
         git add README.md
         git commit -m "Adding README"
         git push origin master
      ```
         
      After a moment, Pipelines recognises the commit to the `k8s-wordpress` repo and starts building the container. When the build is complete, the new container appears in the build stage with a tag that matches the build number. Next, Pipelines updates the containers in each deployment stage in order.  
1. Manually promote your changes to the wordpress-prod stage.
   1. Click **Promote** between the wordpress-stage and wordpress-prod stages.
   1. Select **Deployments**. 
      Pipelines fills the Image fields with the builds from the previous stage in the pipeline.
   1. Click **Promote**. 
      Pipelines creates the deployment and gives it a deployment number.  

Congratulations! You have a working WordPress deployment hosted on Kubernetes, and an automated multi-stage pipeline.

**Related Links**
Continue learning about Pipelines:
* For information about configuring build and deploy notifications, see [Kubernetes notifications](./notification.md).
* For information about cluster authentication, see [Set cluster permissions](./cluster-set-permissions.md).
* For information about Helm charts, see [Helm charts overview](./helm-overview.md). 

