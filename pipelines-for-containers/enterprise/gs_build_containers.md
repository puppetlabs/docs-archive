---
layout: default
title: "Build your containers"
--- 

Now that you have created MySQL and WordPress containers and added them to a project on Pipelines, you’ll use Pipelines to build each of the containers.

## Build your MySQL container
1. In Pipelines, select your project.
1. Click **Build**.
1. Under **Build a container**, select your *k8s-mysql* container.
1. Under **Branch**, select **master**.
1. Click **Build**. 
Pipelines builds your container in the background. If want to watch the process, click **View Build**.

## Build your WordPress container
1. In Pipelines, select your project.
1. Click **Build**.
1. Under **Build a container**, select your *k8s-wordpress* container.
1. Under **Branch**, select **master**.
1. Click **Build**.
Pipelines builds your container in the background. If want to watch the process, click **View Build**.

Now that you’ve built your containers, there is one final step to complete before you deploy them to your Kubernetes cluster: [create a MySQL service](./gs_create_mysql_service.html).
