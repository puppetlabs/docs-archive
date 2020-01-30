---
layout: default
title: "Create a WordPress container"
--- 

You’ve created the MySQL database container that serves your WordPress site. Now it’s time to create the WordPress container. Much like the MySQL container, you’ll define your WordPress container in a Dockerfile, and then push it to a Github repo. Finally, you’ll create a Docker Hub container repo for future builds. 

## Create a WordPress container and push it to Github

1. Create a directory, *k8s-wordpress*, to store your container.

   ```
   mkdir k8s-wordpress 
   cd k8s-wordpress
   ```
   
1. Create a file called *Dockerfile* (no file extension) and put the following code in it:
   
   ```
   FROM wordpress
   VOLUME  ["/var/www/html"]
   EXPOSE 80
   ```
   
1. Log into Github and click **Start a project**.  
1. Name your repo *k8s-wordpress* and click **Create repository**.
1. Push *Dockerfile* to the *k8s-wordpress* repo using the following commands.

   ```
   git init
   git add Dockerfile
   git commit -m "Add new Dockerfile"
   git remote add origin https://github.com/<YOUR_GITHUB_USER_ID>/k8s-wordpress.git
   git push -u origin master
   ```

## Create a repository on Docker Hub

  1. Log into Docker Hub.
  1. Click **Create > Create Repository**.
  1. Name the repo *k8s-wordpress* and enter a short description for it. 
  1. Click **Create**. 

You now have both a MySQL container and a WordPress container defined in GitHub. Next, you’ll [create a Pipelines project and add your containers](./gs_create_project.html). 
