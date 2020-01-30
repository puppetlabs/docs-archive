---
layout: default
title: "Create a project and add containers"
--- 

You’ve created your containers and you’re almost ready to build and deploy them. First, you need to set up a Pipelines project and add your containers to it.

## Create a Pipelines project
1. Log into your Pipelines account.
1. Select **Pipelines for containers** from the Pipelines drop-down menu.
1. Click **New Project**. Enter a project name *WordpressProject* and a brief description.
1. Click **Create Project**.

## Add containers to your project

1. From your project page, click **Add Containers**.
1. Click **Connect Source Control**. Add the MySQL container by specifying these settings:
   * **Source Control**: Select GitHub.
   * **Repository**: Select the repo for your container: `<YOUR_GITHUB_USER_ID>/k8s-mysql`
   * **Container Name**: Enter the name of the container: *k8s-mysql*.
   * **Docker Registry Provider**: DockerHub
   * **Repository**: Select the repo for your container: `<YOUR_DOCKER_USER_ID>/k8s-mysql`
1. Click **Add Container**.
1. Click **Add Container** > **Connect Source Control**. Add the Wordpress container by specifying these settings:
   * **Source Control**: Select GitHub.
   * **Repository**: Select the repo for your container: `<YOUR_GITHUB_USER_ID>/k8s-wordpress`
   * **Container Name**: Enter the name of the container: *k8s-wordpress*.
   * **Docker Registry Provider**: DockerHub
   * **Repository**: Select the repo for your container: `<YOUR_DOCKER_USER_ID>/k8s-wordpress` 
1. Click **Add Container**.

Your MySQL and Wordpress containers are listed in the **Containers** column.

You’ve created your Pipelines project and added your containers to it. Now it’s time to [build the containers in Pipelines](./gs_build_containers.html)