---
layout: default
title: "Add a job to your pipeline"
--- 

There are several ways you can dispatch jobs in a pipeline. You can add a job to an existing stage to dispatch concurrently with a deployment, you can add a job as its own stage, or dispatch multiple jobs concurrently in a stage.

Before you add a job to a pipeline, you must create a job template. For more information about job templates, see [our documentation on jobs](./job.html). 

## Add a job to a pipeline

1. In Pipelines, open your project. 
1. Click **Pipelines**.
1. Create a pipeline or open an existing one.
1. Click **Add Job** to add a job to an existing stage, or click **Add Job Stage** to add a new job stage to the pipeline. 
1. Select your job template. 
   > A default cluster and/or namespace may already be specified in the job template. You can choose a different cluster/namespace to dispatch this job to during pipeline execution.
1. Enter a cluster and namespace.
1. Click **Add Stage**. 

**Related Links**:
* For more information about jobs in Pipelines, see our [jobs documentation](./job.html).
* For more information about setting up a pipeline, see [Set up a multi-stage pipeline](./pipeline-set-up.html).