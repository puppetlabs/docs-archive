---
layout: default
title: "Checking a Puppet job plan"
canonical: "/pe/latest/orchestrator_job_plan.html"
---

The `puppet job plan` command allows you to preview a plan for a Puppet job without actually enforcing any change. Use this command to ensure your job will run as expected. 
 
## View a job plan

Use the `puppet job plan` command to print the plan that would be generated if you ran a Puppet job.

You can preview plans for three types of targets, but these targets cannot be combined:

- View a plan to enforce change on a an application or an application instance in an environment
- View a plan to enforce change on a list of nodes or a single node
- View a plan to enforce change based on a PQL nodes query 

>**Note**: The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](./orchestrator_install.html#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

>**Note:** If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-job plan`.
    
### View a job plan for applications or application instances

You can view a plan for enforcing change on all instances of an application or for a specific instance of an application.

>**Tip:** You can use `-a` in place of `--application`.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To view a job plan for all instances of an application:
     
     ~~~
     puppet job plan --application <APPLICATION> <OPTIONS>
     ~~~
    
   - To view a job plan for an application instance in an environment:
   
     ~~~
     puppet job plan --application <APPLICATION INSTANCE> <OPTIONS>
     ~~~
      
### View a job plan for a list of nodes or a single node

You can view a plan for enforcing change on a single node or a comma-separated list of nodes.

>**Tip:** You can use `-n` in place of `--nodes`.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To view a plan for a single node:
     
     ~~~
     puppet job plan --nodes <NODE NAME> <OPTIONS>
     ~~~
   
   - To view a plan for a list of nodes, use a **comma-separated** list of node names. 
   
     ~~~
     puppet job plan --nodes <NODE NAME>, <NODE NAME>, <NODE NAME>, <NODE NAME> <OPTIONS>
     ~~~
    
### View a job plan for a PQL nodes query

Use a [PQL nodes query]({{puppetdb}}/api/query/tutorial-pql.html) as a Puppet job target. In this case, the orchestrator presents a plan for a job that could run on a list of nodes returned from a PQL nodes query.

>**Tip:** You can use `-q` in place of `--query`.

1. Ensure you have the [correct permissions](./orchestrator_install.html#setting-rbac-permissions) to use PQL queries.

2. Log into your Puppet master or client tools workstation and run the following command:

   ~~~
   puppet job plan --query '<QUERY>' <OPTIONS>
   ~~~

> **Note:** The following table shows some example targets and the associated PQL queries you could run with the orchestrator.
>
>Be sure to wrap the entire query in single quotes and use double quotes inside the query.
>
>Target      | PQL query |
>------------|-----------|
>Single node by certname | `--query 'nodes { certname = "mynode" }'`
>All nodes with "web" in certname | `--query 'nodes { certname ~ "web" }'`
>All CentOS nodes | `--query 'inventory { facts.os.name = "CentOS" }'`
>All CentOS nodes with httpd managed | `--query 'inventory { facts.operatingsystem = "CentOS" and resources { type = "Service" and title = "httpd" } }'`
>All nodes with failed reports | `--query 'reports { latest_report? = true and status = "failed" }'` 
>All nodes matching the  environment for the last received catalog | `--query 'nodes { catalog_environment = "production" }'`
   
## Command options

The following table shows common options for the `puppet job plan` command. For a complete list of options, run `puppet job --help`.

<table>
  <tr>
   <th> Option </th>
   <th> Value </th>
   <th> Description </th>
  </tr>
 <tr>
   <td width="175"> <code> --environment </code>, <code> -e </code></td>
   <td width="175"> Environment name </td>
   <td>
{% md %}
Overrides the environment specified in the orchestrator configuration file. The orchestrator uses this option to:

* Instruct nodes what environment to run in (and if any nodes can't run in the environment, those node runs will fail). A node will run in an environment if:
    * The node is included in an application in that environment. (These runs may fail if the node is classified into a different environment in the PE Classifier.)
    * The node is classified into that environment in the PE Classifier.
* Load the application code used to plan node run oder.
{% endmd %}
   </td>
</tr>
 <tr>
   <td width="175"> <code>--concurrency</code> </td>
   <td width="175"> Integer </td>
   <td>
   {% md %}Limits how many nodes can run concurrently. (Default is unlimited.) You can [tune concurrent compile requests](./config_orchestration.html#configure-the-orchestrator-and-orchestration-services) in the PE console. {% endmd %}</td>
 </tr>
</table>
   
## Reviewing the job plan

The `puppet job plan` command returns the following:

- The environment the job will run in.
- The target for the job.
- The maximum concurrency setting for the job.
- The total number of nodes in the job run.
- A list of application instances included in the job, if applicable.
- A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, level 0, have no dependencies. Nodes in level 1 have dependencies in level 0. A node can run once Puppet has finished running on all its dependencies.

>**Important**: The `puppet job plan` **does not** generate a job ID. A job ID is only created, and shown in the job plan, when you use `puppet job run`.
>
>Use the job ID to view jobs with the [`puppet job show`](./orchestrator_job_status_view.html) command.

### Example job plans

The following is an example plan summary for a job that will run on the entire `production` environment (the default environment). Nodes not participating in an application are displayed at level 0 because they have no dependencies and can run immediately.

<a href="./images/orchestrator_job_plan.png"><img src="./images/orchestrator_job_plan.png" alt="Job Plan" title="Click to enlarge"> (Click to enlarge)</a>

The following is an example plan summary for a job that will run on a list of nodes that contain a partial application instance.

<a href="./images/orchestrator_job_run.png"><img src="./images/orchestrator_job_run.png" alt="Job Plan" title="Click to enlarge"> (Click to enlarge)</a>

After you view the plan:

- Use `puppet job run <SAME TARGET AND OPTIONS>` to create and run a job like this.
- Remember that node catalogs may have changed since this plan was generated.
- Review and address any errors that the service reports it will encounter when running the job. For example, it will detect any dependency cycles or components that not properly mapped to nodes.

