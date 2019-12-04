---
layout: default
title: "Enforcing change with the Puppet orchestrator"
canonical: "/pe/latest/orchestrator_job_run.html"
---

The `puppet job run` command is the recommended way to enforce change on your agent nodes. For example, did you just add a new class parameter to a set of nodes ? Use the `puppet job` tool to enforce that change across those nodes. Or did you just deploy code to a new Puppet environment? Use this command to run Puppet across all the nodes in that environment.

## Enforce change with `puppet job run`

The `puppet job run` command initiates an orchestration job. It creates a new Job ID, shows you all nodes included in the job, and proceeds to run Puppet on all nodes in the appropriate order. Puppet will compile a new catalog for all nodes included in the job.

If you run a job on a node that has relationships outside of the target (for example, it participates in an application that includes nodes not in the job target) the job will still only run on the node in the target you specified. In such cases, the orchestrator notifies you that external relationships exist. It prints the node with relationships, and it prints the applications that may be affected. For example:

~~~
**WARNING** target does not contain all nodes in this application.
~~~

You can run jobs to enforce change on three types of targets, but these targets cannot be combined:

- Enforce change on an application or an application instance in an environment
- Enforce change on a list of nodes or a single node
- Enforce change based on a PQL nodes query 

>**Note**: The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](./orchestrator_install.html#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

>**Note:** If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-job run`.

### Enforce change on an application or an application instance in an environment

Use applications as a Puppet job target to enforce Puppet runs in order on all nodes found in a specific application instance, or to enforce Puppet runs in order on all nodes that are found in each instance of an application.

>**Tip:** You can use `-a` in place of `--application`.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To run a job on all instances of an application:

     ~~~
     puppet job run --application <APPLICATION> --environment <ENVIRONMENT>
     ~~~

   - To run a job on an instance of an application in an environment:

     ~~~
     puppet job run --application <APPLICATION INSTANCE> --environment <ENVIRONMENT>
     ~~~

### Enforce change on a list of nodes or a single node

Use a single node or a comma-separated list of nodes as a Puppet job target.

>**Tip:** You can use `-n` in place of `--nodes`.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To run a job on a single node:

     ~~~
     puppet job run --nodes <NODE NAME> <OPTIONS>
     ~~~

   - To run a job on a list of nodes, use a **comma-separated** list of node names:

     ~~~
     puppet job run --nodes <NODE NAME>,<NODE NAME>,<NODE NAME>,<NODE NAME> <OPTIONS>
     ~~~

     >**Important:** Do not add spaces in the list of nodes.


### Enforce change based on a PQL nodes query

Use a [PQL nodes query]({{puppetdb}}/api/query/tutorial-pql.html) as a Puppet job target. In this case, the orchestrator runs on a list of nodes returned from a PQL nodes query.

>**Tip:** You can use `-q` in place of `--query`.

1. Ensure you have the [correct permissions](./orchestrator_install.html#setting-rbac-permissions) to use PQL queries.

2. Log into your Puppet master or client tools workstation and run the following command:

   ~~~
   puppet job run --query '<QUERY>' <OPTIONS>
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

The following are common options you may use with the `run` action. For a complete list of global options run `puppet job --help`.

<table>
  <tr>
   <th> Option </th>
   <th> Value </th>
   <th> Description </th>
  </tr>
 <tr>
   <td width="175"> <code>--noop</code> </td>
   <td width="175"> Flag, default false </td>
   <td> Run a deployment job without executing the changes. </td>
 </tr>
 <tr>
   <td width="175"> <code>--environment -e</code> </td>
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
   <td width="175"> <code>--no-enforce-environment</code> </td>
   <td width="175"> Flag, default false </td>
   <td> Ignores the environment set by the <code>--environment</code> flag for agent runs. When you use this flag, agents run in the environment specified by the PE Node Manager or their <code>`puppet.conf`</code> file. </td>
 </tr>
 <tr>
   <td width="175"> <code>--concurrency</code> </td>
   <td width="175"> Integer </td>
   <td>
   {% md %}Limits how many nodes can run concurrently. (Default is unlimited.) You can [tune concurrent compile requests](./config_orchestration.html#tuning-orchestrator-concurrent-compile-requests) in the PE console. {% endmd %}</td>
 </tr>
</table>

## Reviewing the job plan with `puppet job run`

When you execute a `puppet job run` command, the orchestrator shows the plan for the run and immediately runs the job.

Job plan details, and examples, are documented in [Reviewing the job plan](./orchestrator_job_plan.html#reviewing-the-job-plan).

>**Important**: The `puppet job plan` **does not** generate a job ID. A job ID is only created, and shown in the job plan, when you use `puppet job run`. 
>
>Use the job ID to view jobs with the [`puppet job show`](./orchestrator_job_status_view.html) command.

## Post-run node status

After you run a Puppet job, the command returns a list of nodes and their run statuses. 

Node runs can be **in progress**, **completed**, **skipped**, or **failed**.

- For a **completed** node run, the orchestrator prints the following information:
   - The configuration version.
   - The transaction ID.
   - A summary of resource events.
   - A link to the full node run report in the PE console.

- For an **in progress** node run, the orchestrator prints how many seconds ago the run started.

- For a **failed** node run, the orchestrator prints an error message indicating why the run failed. In this case, any additional runs will be **skipped**.

When a run fails, the orchestrator also prints what applications were affected by the failure, as well as any applications that were affected by skipped node runs.

You can view the status of all running, completed, and failed jobs with the `puppet job show` command.

## Terminating jobs

To terminate a running job, use `CTRL+C`.

When you terminate a job, any Puppet runs that are underway will finish. Any runs that have not started will not start.

>**Note:** orchestrator jobs on Windows agents can't be terminated with `CTRL+C`. If you need to stop an orchestration job running on a Windows agent, you can use the [/command/stop API endpoint](./orchestrator_api_commands.html#post-commandstop). <!--ORCH-1080-->

## Analyzing changes and events in the PE console

The PE console helps you [investigate the current state of your infrastructure](./CM_events.html) by drilling down into failures and other changes. The [node graph](./CM_graph.html) provides a graph view of a node's catalog to help you locate and solve problems by finding which resources are causing nodes to fail or which resources have changed.

Use these tools for a closer look at successes and failures in your Orchestration runs.