---
layout: default
title: "Create MySQL services"
--- 

Your containers are built and you’ve provisioned a Kubernetes cluster. Before you deploy your containers to the cluster, set up communication between them by creating a service to expose TCP port 3306 from the MySQL container to the cluster. After the MySQL container is deployed, other deployments within the cluster, specifically the WordPress container, are  able to discover this service, either through environment variables, or through domain name service (DNS). You're going to deploy your containers to all three of the namespaces you've created, so each namespace also needs it's own service.

1. Create a service on the wordpress-dev namespace:
   1. From your Pipelines project page, click **Services**.
   1. Select the cluster you set up in the first section of this guide.
   1. Click **Create a new service**. Your service needs to define the following fields:
      * The name of the service: *mysql-service*
      * The namespace where you're deploying the service: *wordpress-dev*
      * The deployment you're attaching the service to: *k8s-mysql* is the deployed container that you're targeting with this service.
      * The container's port (port): *3306* 
      * The target port (targetPort): *3306* is the target port on the cluster where Wordpress looks for MySQL.
   1. Paste the following text into the field:
        
```
apiVersion: v1
kind: Service
metadata:
 name: k8s-mysql
 namespace: wordpress-dev
spec:
 selector:
  deployment: k8s-mysql
 ports:
 - name: default
   protocol: TCP
   port: 3306
   targetPort: 3306
```

1. Create a service for MySQL on the wordpress-stage namespace:
   1. Click **Create a new service**.
   1. Paste the following YAML configuration into the console.     
        
```
apiVersion: v1
kind: Service
metadata:
 name: k8s-mysql
 namespace: wordpress-dev
spec:
 selector:
  deployment: k8s-mysql
 ports:
 - name: default
   protocol: TCP
   port: 3306
   targetPort: 3306
```
        
   1. Click **Create Service**.
1. Create a service for MySQL on the wordpress-prod namespace:
   1. Click **Create a new service**.
   1. Paste the following YAML configuration into the console.
        
```
apiVersion: v1
kind: Service
metadata:
 name: k8s-mysql
 namespace: wordpress-dev
spec:
 selector:
  deployment: k8s-mysql
 ports:
 - name: default
   protocol: TCP
   port: 3306
   targetPort: 3306
```
        
   1. Click **Create Service**.
   
You've created your services, enabling your containers to communicate with each other after they're deployed. Next, [deploy your containers](./gs_deploy_containers.html).    
