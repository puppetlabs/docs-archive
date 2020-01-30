---
layout: default
title: "Kubernetes jobs"
---

A Kubernetes job is a finite-length task that runs to completion. A job doesn&rsquo;t run continuously on a cluster, and doesn&rsquo;t require ongoing management from Kubernetes. After all of its pods have successfully terminated, they close down, and the job is complete; Kubernetes does not restart the pods.

If you&rsquo;re building something that runs continually on a cluster, and requires ongoing management from Kubernetes, see [Deployments](./deployment-set-up.html).

With jobs in Pipelines for Containers, you can dispatch a job manually, or dispatch it automatically inside a pipeline. If you&rsquo;re working on a team, you&rsquo;ll always know who ran a job, and what was run. You also have access to logs for each job.

## Create a job

1. In Pipelines, select your project.
1. Click **Jobs**.
1. Click **Create New Job**.
1. To create the job from an existing Kubernetes job specification:
   1. Click **YAML** and paste in your YAML specification.
   1. Click **Save Job**.
1. Otherwise, to create a job:
   1. Click **Select Cluster**, and select your cluster. 
   1. Click **Select Namespace** and select a namespace. 
   1. Enter a job name and a helpful description.
   1. Click **Containers** and complete each of these fields:
      * **Container Name**: A unique name for the container.
      * **Container Image**: An image to pull to run the job command, for example: `gcr.io/<YOUR_PROJECT_NAME>/<YOUR_IMAGE>:tag` or `mysql:5.7`.  
      * **Command**: The command to run when the image spins up. This is the job. For example: `perl`.
      * **Args**: Enter the arguments used with the above command, for example:
        
        ```
        -Mbignum=bpi
        -wle
        -print bpi(2000)
        ````

    1. Click **Add Container**.
    1. (Optional) Select the container you just added and configure port mappings, volume mounts, and environment variables. 
    1. Click **Configuration** and set your job parameters:
       * **Active Deadline Seconds**: The time in seconds, relative to its start time, that the job can be active before the system tries to terminate it.
       * **Completions**: The number of pods that need to run to completion before the job is finished.
       * **Parallelism**: The maximum number of pods the job runs at any given time.
       * **Restart Policy**: The restart policy for all containers within the pod. This can be **Never** or **OnFailure**.
    1. If you specified volume mounts for your containers, click **Volumes** to add them to your Job. 
    1. Click **Save Job**.

You've created your job and configured its default parameters. You can dispatch it manually, or automatically by adding it to a pipeline. To change the parameters for a job, find it on the **Jobs** page and click **Edit Job**.

## Run a job manually
    
After you’ve created a job, you can dispatch it manually.
1. In Pipelines, select your project.
1. Click **Jobs**.
1. Click **Dispatch Job** next to the job you want to dispatch.
1. (Optional) Adjust the values for this specific job. Parameters you change now will return to their previous values on future dispatches.
1. Click **Dispatch Job** and wait for the verification that your job ran successfully.

> **Note**: You can cancel a running job by clicking Cancel Job.

## View job history and details

To view a running, or previously dispatched job’s history, select it on the **Jobs** page. Click on an instance in the **Job History** column to see more details about that specific dispatch. These details include the parameters used, the job status, and job logs.

## View a job log

After a job has completed, Pipelines gathers logs from the job’s containers, and deletes the job and its pods from the cluster. 

To view a job’s logs:
1. In Pipelines, select your project.
1. Click **Jobs**.
1. Select a job dispatch in the **Job History** column.
1. Click **Show Log** next to the container you want to view.

