---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Viewing job plans

The `puppet job plan` command allows you to preview a plan for a Puppet job without actually enforcing any change. Use this command to ensure your job runs as expected.

You can preview plans for three types of targets, but these targets cannot be combined:

-   View a plan to enforce change on a list of nodes or a single node

-   View a plan to enforce change based on a PQL nodes query

-   View a plan to enforce change on an application or an application instance in an environment


## Results from the job plan command

The `puppet job plan` command returns the following about a job:

-   The environment the job runs in.
-   The target for the job.
-   The maximum concurrency setting for the job.
-   The total number of nodes in the job run.
-   A list of application instances included in the job, if applicable.
-   A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, level 0, have no dependencies. Nodes in level 1 have dependencies in level 0. A node can run once Puppet has finished running on all its dependencies.

**Note:** The `puppet job plan` **does not** generate a job ID. A job ID is created, and shown in the job plan, only when you use `puppet job run`. Use the job ID to view jobs with the `puppet job show` command.

After you view the plan:

-   Use puppet job run <SAME TARGET AND OPTIONS\> to create and run a job like this.
-   Remember that node catalogs might have changed since this plan was generated.

-   Review and address any errors that the service reports. For example, it detects any dependency cycles or components that not properly mapped to nodes.


## Example job plan

The following is an example plan summary for a job that runs on a list of nodes that contain a partial application instance.

![Screen shot of puppet job plan run on the command line.](orchestrator_job_plan.png)

## View a job plan for a list of nodes or a single node

You can view a plan for enforcing change on a single node or a comma-separated list of nodes.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run tasks and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To view a plan for a single node: `puppet job plan --nodes <NODE NAME> <OPTIONS>`
    -   To view a plan for a list of nodes, use a **comma-separated** list of node names. `puppet job plan --nodes <NODE NAME>, <NODE NAME>, <NODE NAME>, <NODE NAME> <OPTIONS>`
    **Tip:** You can use `-n` in place of `--nodes`.


## View a job plan for a PQL nodes query

Use a PQL nodes query as a job target. In this case, the orchestrator presents a plan for a job that could run on a list of nodes returned from a PQL nodes query.

### Procedure

1.  Ensure you have the correct permissions to use PQL queries.

2.  Log into your Puppet master or client tools workstation and run the following command: `puppet job plan --query '<QUERY>' <OPTIONS>`

    **Tip:** You can use `-q` in place of `--query`.

    The following table shows some example targets and the associated PQL queries you could run with the orchestrator.

    Be sure to wrap the entire query in single quotes and use double quotes inside the query.

    |Target|PQL query|
    |------|---------|
    |Single node by certname|`--query 'nodes { certname = "mynode" }'`|
    |All nodes with "web" in certname|`--query 'nodes { certname ~ "web" }'`|
    |All CentOS nodes|`--query 'inventory { facts.os.name = "CentOS" }'`|
    |All CentOS nodes with httpd managed|`--query 'inventory { facts.operatingsystem = "CentOS" and resources { type = "Service" and title = "httpd" } }'`|
    |All nodes with failed reports|`--query 'reports { latest_report? = true and status = "failed" }'`|
    |All nodes matching the environment for the last received catalog|`--query 'nodes { catalog_environment = "production" }'`|


## View a job plan for applications or application instances

You can view a plan for enforcing change on all instances of an application or for a specific instance of an application.

### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To view a job plan for all instances of an application: `puppet job plan --application <APPLICATION> <OPTIONS>`
    -   To view a job plan for an application instance in an environment: `puppet job plan --application <APPLICATION INSTANCE> <OPTIONS>`
    **Tip:** You can use `-a` in place of `--application`.


## Puppet job plan command options

The following table shows common options for the `puppet job plan` command. For a complete list of options, run `puppet job --help`.

|Option|Value|Description|
|------|-----|-----------|
|`--environment`, `-e`|Environment name|Overrides the environment specified in the orchestrator configuration file. The orchestrator uses this option to: -   Instruct nodes what environment to run in. If any nodes can’t run in the environment, those node runs fail. A node runs in an environment if:

-   The node is included in an application in that environment. These runs may fail if the node is classified into a different environment in the PE node classifier.

-   The node is classified into that environment in the PE node classifier.

-   Load the application code used to plan run order


|
|--`concurrency`|Integer|Limits how many nodes can run concurrently. Default is unlimited. You can tune concurrent compile requests in the console.|

