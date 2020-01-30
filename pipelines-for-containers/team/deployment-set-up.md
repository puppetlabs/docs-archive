---
layout: default
title: "Set up a deployment"
--- 

With Pipelines for Containers, you build containers for your applications and publish them to a Kubernetes cluster using deployments. A deployment keeps one or more containers running in a pod, restarts the containers if they exit, and terminates them if they fail health checks. Use a deployment when you’re creating a service or container that needs to run continually on your cluster.

If you’re building a finite-length task — something that runs to completion and does not require Kubernetes to restart it if it exits successfully — see [Jobs](./job.html).

Before you set up a deployment, make sure you’re familiar with the Pipelines for Containers workflow. For more information, see Getting started with [Pipelines for Containers](./gs_overview.html).

## Prerequisites

Before you set up a deployment:
* Create or connect a [Kubernetes cluster](./cluster-add.html) with [namespaces](./cluster-namespace.html) for your deployment. 
* Set up a [Pipelines project](./project.html).
* [Build all of the containers](./container.html) that you need for your application and add them to your project.

## Set up a deployment

1. Create a deployment:
   1. In Pipelines, select your project and click **Deploy**.
   1. Click **Select Cluster**, and select your cluster. 
   1. Click **Select Namespace** and select a namespace. 
   1. Enter a deployment name and a helpful description.
1. Add containers to the deployment: 
   1. Click **Select Container** and select a container to add.
   1. Click **Select Image Tag** and select the most recent build. The most recent build is always at the top of the list.
   1. Click **Add Container**. The container you just added is listed under the Containers tab.
   1. (Optional) Click the container and configure its port mappings, volume mounts, and environment variables.

   > **Important**: Repeat these steps until you’ve added all the containers you need.

1. Click **Configuration** and check the settings for your deployment:

   <table>
   <tr>
       <td><b>Replicas</b></td>
       <td>Set the number of identical pods to create for your deployment.</td>
   </tr>
   <tr>
       <td><b>Deployment Strategy</b></td>
       <td><b>Rolling Update</b>: Incrementally replace existing pods. with new ones to eliminate downtime.
           <br/><b>Recreate</b>: Remove existing pods and replace them with new versions. This option causes downtime each time you deploy.
       </td>
       </tr>
       <tr>
           <td><b>Max Unavailable</b></td>
           <td>The maximum number of pods that can be unavailable during the update process.</td>
       </tr>
       <tr>
           <td><b>Max Surge</b></td>
           <td>The maximum number of pods that can be scheduled above the desired number of pods during the update process.</td>
       </tr>
       <tr>
           <td><b>Minimum Ready Seconds</b></td>
           <td>If the application you’re deploying requires extra time to boot up, enter the time in seconds that Kubernetes should wait before the newly created pod is considered available.</td>
       </tr>
       <tr>
           <td><b>Revision History Limit</b></td>
           <td>The number of old replica sets to retain to allow for rollback.</td>
       </tr>
   </table>

1. (Optional) If you specified volume mounts for your containers, click **Volumes** to add them to your deployment.
   > **Note**: For more on volumes, see [Connect a container to a volume](./pfc_volumes.md)   
1. (Optional) To view the YAML generated from your settings, click **YAML**.
1. Click **Deploy** and allow some time for the containers to deploy and connect. 

You’ve deployed your containers. Kubernetes automatically manages your deployment to maintain the configuration that you specified above. 

**Related Links**:
* To find out how to automatically update your deployments with new container versions as they’re built, see [Set up a multi-stage pipeline](./pipeline-set-up.html).
* For information about jobs, see [Jobs](./job.html). 
* For information about configuring build and deploy notifications, see [Kubernetes notifications](./notification.html).
* For information about adding secrets to your container environment variables, see [Create a secret](./pfc_secrets).
