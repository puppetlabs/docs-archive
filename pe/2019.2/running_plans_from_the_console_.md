---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Running plans from the console

Run ad hoc plans from the console.

## Before you begin

Install the plans you want to use.

Make sure you have permission to run the plans.

## About this task

When you set up a job to run a plan from the console, the orchestrator creates a job ID to track the job, shows the nodes included in the job, and runs the plan on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job.

**Note:** Running a plan does not update your Puppet configuration. If you run a plan that changes the state of a resource that Puppet is managing \(such as upgrading a service or package\), a subsequent Puppet run changes the state of that resource back to what is defined in your Puppet configuration.

## Procedure

1.  In the console, in the **Run** section, click **Plan**.

2.  Under **Job description**, name the job that is running the plan something unique so you can find it later in the job details page.

3.  Select any parameters you want to include. For example, **nodes**.

    **Note:** To find out what parameters can be included in a plan, view the plan using the command line. For more information, see [Inspecting plans](inspecting_plans.md)

4.  After you have added parameters, click **Run job**.


## Results

Your plan status and output will appear in the **Job** section on the **Plan** tab.

