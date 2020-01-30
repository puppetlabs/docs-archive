---
layout: default
title: "Working with jobs"
--- 

## Create a job

Create a new job in the Pipelines web UI. 

<ol> 
  <li>In the web UI navigation bar, click <b>Jobs</b>. On the Jobs page, click <b>Create New Job</b>.</li>
  <li>Enter a <b>Job Name</b> and optional description for your new job.</li>
  <li>In the <b>Job</b> terminal box, enter shell commands for the job to run.</li>
  <li>(Optional.) In the <b>AfterJobSuccess</b> terminal box, enter shell commands you wish to run if the job succeeds.</li>
  <li>(Optional.) In the <b>AfterJobFailure</b> terminal box, enter shell commands you wish to run if the job fails.</li>
  <li>In the <b>Job Hardware</b> section, select whether the job should run on your hardware or on shared hardware.</li>
  <li>If using shared hardware, select an <b>Image</b> to run your job in from the choices provided.</li>
  <li>(Optional.) In the <b>Environment Variables</b> text box, specify applicable environment variables as key-value pairs.</li>
  <li>Click <b>Create Job</b>.</li> 
</ol>

Your job is now ready to be dispatched manually or added to a pipeline.

## Dispatch a job

After you've created a job, you can run it manually, otherwise known as dispatching the job. 

When dispatching a job, you can change the job's values for that job run. The job will revert to its saved job template values on future runs.

When you want to dispatch a job, use the dispatch job pane in the Pipelines web UI. 

<b>Before you begin:</b> Make sure you have created a job.

<ol> 
  <li>In the web UI navigation bar, click <b>Jobs</b>.</li>
  <li>In the list of jobs, locate the name of the job you want to dispatch, then click the <b>Dispatch Job</b> icon to the right of the job's name.</li>
  <li>(Optional.) If needed, you can adjust any of the values for this specific job dispatch. These values will be reset to the saved job template on next job dispatch.</li>
  <li>Click <b>Dispatch Job</b>.</li>
</ol>

When your job's run is complete, you'll see either a success message or an error message explaining any run failures.

## Editing a job

After you've created a job, you can easily make changes by using the job editing pane in the Pipelines web UI. 

If you wish to make changes to a job that will apply for only a single job run, see the **Dispatching a job** section.

<ol> 
  <li>In the web UI navigation bar, click <b>Jobs</b>.</li>
  <li>In the list of jobs, locate the name of the job you wish to edit, then click the <b>Edit Job</b> icon to the right of the job's name.</li>
  <li>Make the needed changes.</li>
  <li>When you're finished editing, click <b>Save Job</b>.</li>
</ol>

## Add a job to a pipeline

To deploy a job automatically, add it to an app pipeline in the Pipelines web UI. 

<b>Before you begin:</b> Make sure you have created a job.

<ol> 
  <li>In the web UI navigation bar, click <b>Applications</b>.</li>
  <li>In the list of applications, locate and click the name of the application you want to add the job to.</li>
  <li>In the <b>App Pipeline</b> pane at the right of the screen, decide where you want to add the job to the pipeline. As appropriate, click <b>Add Target</b> in an existing step, or click <b>Add Step</b>.</li>
  <li>In the <b>Control Room</b> panel, click <b>Existing Job</b> and select the job you wish to add to the pipeline, then click <b>Add Target</b>.</li> 
</ol>

You've successfully added the job to your pipeline. For information on configuring auto-deployment of the job, see [Enabling Auto Deploy](./application-deploy.html).

## View job history and run logs

Learn more about a job's run history by viewing summary job history tables and run logs.

To view a job's run history and individual run logs, use the Puppet Pipelines for Applications web UI. 

<ol> 
  <li>In the web UI navigation bar, click <b>Jobs</b>.</li>
  <li>In the list of jobs, locate the name of the job you wish to learn more about, then click the <b>View Job History</b> icon to the right of the job's name. A summary of the job's recent runs appears in the <b>Job History</b> pane.</li>
  <li>To view a job's details and run log, open the <b>Job Details</b> page by clicking an entry in the <b>Job History</b> list.<br>You can view and download the job run's log from the <b>Job Details</b> page.</li>
</ol>
