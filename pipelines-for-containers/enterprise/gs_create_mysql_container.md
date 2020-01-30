---
layout: default
title: "Create a MySQL container"
--- 

Now that you have a cluster set up to store your containers in, you’ll define your MySQL Docker container in a Dockerfile and push it to GitHub. Using Github allows you to efficiently build and deploy your container from within Pipelines. You’ll also create a container repository on Docker Hub so that Pipelines has a place to store and version your future builds. 

## Prerequisites

Make sure you’ve set up accounts for the following:
* <a href ="https://github.com/" target="_blank_">Github</a>
* <a href ="https://hub.docker.com/" target="_blank_">Docker Hub</a>

Ensure that you have <a href ="https://docs.docker.com/docker-hub/github/" target="_blank_">linked your Docker Hub and Github Accounts</a>.

Ensure you have a Git command line tool and a text editor installed.

## Create a MySQL container and push it to Github

1. Create a directory, *k8s-mysql*, to store your container:

   ```
   mkdir k8s-mysql 
   cd k8s-mysql
   ```
	
1. Create a file called *Dockerfile* (no file extension) and put the following code in it:

   ```
   FROM mysql:5.7
ENV MYSQL_DATABASE=wordpress 
RUN apt-get update && \
apt-get install -y sed
RUN sed -i "s|#bind-address\t= 127.0.0.1|bind-address = 0.0.0.0|g" /etc/mysql/mysql.conf.d/mysqld.cnf
VOLUME  ["/etc/mysql", "/var/lib/mysql"]
EXPOSE 3306
   ```

1. Log into Github and click Start a project.  
1. Name your repo *k8s-mysql* and click **Create repository**.
1. In Terminal, push *Dockerfile* to the *k8s-mysql* repo. From the *k8s-mysql* directory, run:

   ```
   git init
   git add Dockerfile
   git commit -m "Add new Dockerfile"
   git remote add origin https://github.com/<YOUR_GITHUB_USER_ID>/k8s-mysql.git
   git push -u origin master
   ```

## Create a repository on Docker Hub

1. Log into Docker Hub.
1. Click **Create > Create Repository**.
1. Name the repo *k8s-mysql* and enter a short description for it.
1. Click **Create**. 

You’ve created your MySQL container! Next, [you’ll create the WordPress container](./gs_create_wordpress_container.html) that goes with it.
