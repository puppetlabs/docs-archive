---
layout: default
title: "Checking a Puppet job plan"
canonical: "/pe/latest/orchestrator_job_plan.html"
---

Use the `puppet job plan` command to print the plan that would be generated if you ran a Puppet job. 

The command returns the following:

- The environment the job will run in.
- The target for the job—an application or an application instance.
- The concurrency setting for the job.
- The total number of nodes in the job run.
- A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, level 0, have no dependencies. Nodes in level 1 have dependencies in level 0. A node can run once Puppet has finished running on all its dependencies.

>**Note**: Job run summaries that do not execute applications will not show any information about applications and components.

## Viewing the job plan

To view the job plan for a run across an entire environment:

   `puppet job plan <OPTIONS>`
   
   If you don’t specify any options, Puppet will show a plan for all nodes in the default Puppet environment, as specified in your orchestrator configuration file.

To view the job plan for all instances of an application in an environment:

   `puppet job plan <APPLICATION> <OPTIONS>`

To view the job plan for an application instance in an environment:

   `puppet job plan <APPLICATION INSTANCE> <OPTIONS>`
   
### Command options

The following table shows common options for the `puppet job plan` command. For a complete list of options, run `puppet job --help`.

Option | Values | Description
-----------|-----------|-----------
`--environment`, `--env`, `-e`| Environment name | Overrides the environment specified in your orchestrator configuration file.
`--concurrency`, | Integer |  Limits how many nodes can run concurrently. (Default is unlimited.)
   
### Example job plan

The following is an example plan summary for a job that will run on the entire `production` environment (the default environment). Nodes not participating in an application are displayed at level 0 because they have no dependencies and can run immediately.

<a href="./images/orchestrator_job_plan.png"><img src="./images/orchestrator_job_plan.png" alt="Job Plan" title="Click to enlarge"> (Click to enlarge)</a>

After you view the plan::

- Use `puppet job run <SAME TARGET AND OPTIONS>` to create and run a job like this.
- Remember that node catalogs may have changed since this plan was generated.
- Review and address any errors that the service reports it will encounter when running the job. For example, it will detect any dependency cycles or components that not properly mapped to nodes.

>**Note**: Running `puppet job plan` **does not** generate a job ID.
