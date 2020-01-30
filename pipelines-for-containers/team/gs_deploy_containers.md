---
layout: default
title: "Deploy your WordPress and MySQL containers"
--- 

In previous steps, you provisioned a cluster, created and built MySQL and WordPress containers, and created a service to ensure that your containers are able to communicate within your cluster. Next, you’ll deploy your containers to all three of the namespaces that you created on your cluster.

## Deploy to wordpress-dev

First, deploy your containers to the wordpress-dev namespace. After you've created your first deployment, you can save time by using it as a template for future deployments.

1. Enter your deployment details:
   1. From your Pipelines project, click **Deploy**.
   1. Click **Select Cluster**, and select your cluster. 
   1. Click **Select Namespace** and select your namespace, wordpress-dev. 
   1. Enter a deployment name, *my-wordpress-deployment*, and a short description.
1. Add and configure your WordPress container:
   1. Click **Select Container** and select your WordPress container, k8s-wordpress.
   1. Click **Select Image Tag** and select the most recent build. At this point there should be only one, but the most recent build is always at the top of the list.
   1. Click **Add Container**. The k8s-wordpress container is listed under the Containers tab.
   1. Click **k8s-wordpress**.
   1. Click **Port Mappings** and then click **Add Port Mapping**. Add a mapping with the following settings:
      * **Name**: *http*
      * **Container Port**: *80*
      * **Host Port**: *80*
      * **Protocol**: *TCP*
   1. Click **Save Port Mapping**.
   1. Click **Environment Variables**. Use the following settings:
      * **Environment Variable Name**: *WORDPRESS_DB_PASSWORD*
      * **Type of Variable**: value
      * **Value**: Enter a password for your container.
   1. Click **Save Variable**.
   1. Click **Environment Variables**. Use the following settings:
      * Environment Variable Name: *WORDPRESS_DB_HOST*
      * Type of Variable: value
      * Value: *k8s-mysql:3306*
   1. Click **Save Variable**.
1. Add and configure your MySQL container
   1. Click **Containers**.
   1. Click **Select Container** and select your MySQL container, k8s-mysql.
   1. Click **Select Image Tag** and select the most recent build.
   1. Click **Add Container**. The k8s-mysql container appears under the Containers tab.
   1. Click **k8s-MySQL**.
   1. Click **Port Mappings** and then click **Add Port Mapping**. Add a mapping with the following settings: 
      * **Name**: *mysql*
      * **Container Port**: *3306*
      * **Host Port**: *3306*
      * **Protocol**: *TCP*
   1. Click **Save Port Mapping**.
   1. Click **Environment Variables**. Enter the following settings:
      * **Environment Variable Name**: *MYSQL_ROOT_PASSWORD*
      * **Type of Variable**: value
      * **Value**: Use the same password that you set for WORDPRESS_DB_PASSWORD above.
   1. Click **Save Variable**.

      > **Tip**: Clicking **YAML** gives you a YAML configuration generated from the deployment options you've selected. This is a good way to save deployments for reference, or for future use in other projects. 
   
   1. Click **Deploy** and a notification appears. To see the progress and details of your deployment, click **Click here**. 
   
## Deploy to wordpress-stage and wordpress-prod

Now that you've created your first deployment, you can use it as a template for your deployments to the wordpress-stage, and wordpress-prod namespaces. 
1. Deploy to the wordpress-stage namespace.
   1. From your Pipelines project, click **Deploy** <img src="images/redeploy.png" alt="Deploy"/> next to your wordpress-dev deployment. 
   1. Change the **Namespace** from **wordpress-dev** to **wordpress-stage**. 
   1. Enter a short deployment description. 
      > **Remember**: You don't need to change any other values. You want this deployment to exactly match your wordpress-dev deployment.
   1. Click **Deploy**.
1. Deploy to the wordpress-prod namespace.
   1. From your Pipelines project, click **Deploy** <img src="images/redeploy.png" alt="Deploy"/>next to your wordpress-dev deployment. 
   1. Change the **Namespace** from **wordpress-dev** to **wordpress-prod**. 
   1. Enter a short deployment description.
   1. Click **Deploy**.

Your containers are deployed. You now have a working WordPress blog hosted on Kubernetes, Next, [automate your builds and deployments in a pipeline](./gs_pipeline_set_up.md).

