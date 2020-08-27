---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Start testing module code with jobs

Jobs are customizable tests for your Puppet code. In this section of the getting started guide, we'll show you how to write a new job, install dependencies on your job hardware, and dispatch your job on demand.

## Before you begin

Make sure you're ready to get started by completing the following:

1.  Designate your testing hardware. See [Configure job hardware](configure_job_hardware.md#) for instructions.


## What is a job?

In Continuous Delivery for PE, jobs are tests for your Puppet code. Jobs are created and stored in the web UI, where you can run them on demand and add them to an automated pipeline that runs tests every time new code is committed to a repository.

Jobs are fully customizable and can be written to test any aspect of your code. Jobs can be written to take advantage of Puppet Development Kit \(PDK\) and other testing tools created and maintained by the Puppet community, such as:

-   rspec-puppet

-   onceover

-   puppet-lint

-   rspec-puppet-facts


It's important to be aware of any prerequisites or dependencies needed to run your jobs, and to ensure that your job hardware has the necessary software, operating systems, and other resources installed before attempting to run the job.

## Create a new job

Use the Continuous Delivery for PE web UI to set up a reusable job, which you can use to test your Puppet code on demand or automatically on every new commit as part of a pipeline.

### About this task

For purposes of this getting started guide, we'll create a simple job that uses Puppet Development Kit to validate your module code. You can use this same process to create cutomized jobs of your own based on your team's testing needs and practices.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Jobs**, then click **New Job**.

2.  In the **Job Name** field, enter module-pdk-validate.

3.  In the **Description** field, enter Validate via the PDK.

4.  In the **Job** field, copy and paste the command that matches your job hardware's operating system:

    For \*nix: `pdk validate`

    For Windows: `powershell.exe -c "pdk validate"`

    **Tip:** In this area of the job creation pane, you can add choose to add additional commands to run in the event of a job success or failure.

5.  In the Capabilities field, enter PDK and PUPPET-AGENT. This ensures that this job will only run on job hardware that's been tagged with these capabilities.

    We'll ensure our job hardware has been set with these capabilities in the next section.

6.  Click **Create Job**. You'll see a message that your job has been successfully created. Click **Go to Jobs**.


### Result:

Your new job is now listed on the Jobs page, and is almost ready to be dispatched. First, however, we need to make sure that job's software dependencies have been met.

## Install job dependencies

In order to run a job successfully, the selected job hardware must have any required dependencies installed, such as software or operating systems.

### About this task

In order to run the jobs we created in the previous step, Puppet Development Kit and the Puppet agent must be installed on your job hardware.

### Procedure

1.  Follow the [Installing PDK](https://puppet.com/docs/pdk/latest/pdk_install.html) instructions appropriate to your job hardware.

2.  Follow the [Installing agents](https://puppet.com/docs/pe/latest/installing/installing_agents.html) instructions appropriate to your job hardware.

3.  In the Continuous Delivery for PE web UI, click **Jobs**, then **Hardware**. Locate your job server.

4.  Click **+ Add Capability** and enter PDK, then click **Save**.

5.  Click **+ Add Capability** and enter PUPPET-AGENT, then click **Save**.


### Result:

## Dispatch a job

Manually running a test on your module code is known as dispatching a job in Continuous Delivery for PE.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Jobs**.

2.  Locate a job in your jobs list that you wish to run and click **Dispatch Job** ![](dispatch.png).

3.  Review the job's details and make any needed adjustments to the commands, capabilities, or environment variables.

4.  Click **Dispatch Job**. Continuous Delivery for PE dispatches your job, and returns a success or failure message.

5.  Click **Go to Job Details**.

    On the Job Details page, you can view and download the log of your job's run.

6.  When you're finished reviewing the job's details, click **Jobs** to return to the main Jobs page. You'll see a record of the job you just manually dispatches has been added to the Job History column.

    To review the details of a previous manual dispatch, click the job's number.


### Result:

You can now run tests on your module code on demand. Next, in the [Start building your module's pipeline](start_building_your_modules_pipeline.md#) guide, we'll learn how to set up a pipeline, so that tests run automatically on every new commit to your module repo.

