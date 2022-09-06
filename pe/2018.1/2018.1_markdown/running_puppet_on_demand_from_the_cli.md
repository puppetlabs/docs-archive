---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running Puppet on demand from the CLI

Use the **puppet job run** command to enforce change on your agent nodes with on-demand Puppet runs.

## About this task

Use the **puppet job** tool to enforce change across nodes. For example, when you add a new class parameter to a set of nodes or deploy code to a new Puppet environment, use this command to run Puppet across all the nodes in that environment.

If you run a job on a node that has relationships outside of the target \(for example, it participates in an application that includes nodes not in the job target\) the job will still only run on the node in the target you specified. In such cases, the orchestrator notifies you that external relationships exist. It prints the node with relationships, and it prints the applications that may be affected. For example:

```
**WARNING** target does not contain all nodes in this application.
```

You can run jobs on three types of targets, but these targets cannot be combined:

-   An application or an application instance in an environment
-   A list of nodes or a single node
-   A PQL nodes query

When you execute a `puppet job run` command, the orchestrator creates a new Job ID, shows you all nodes included in the job, and proceeds to run Puppet on all nodes in the appropriate order. Puppet compiles a new catalog for all nodes included in the job.

The orchestrator command line tool includes the `puppet job` and `puppet app` commands.

-   `puppet job`

    The `puppet job` command is the main mechanism to control Puppet jobs. For a complete reference of the `puppet job` command, see:

    -   Checking job plans on the CLI
    -   Running jobs on the CLI
    -   Reviewing jobs on the CLI
-   `puppet app`

    The `puppet app` tool lets you view the application models and application instances you’ve written and stored on your Puppet master. For a complete reference of the `puppet app` command, see Reviewing applications on the CLI.


## Run Puppet on on a PQL query

Use a PQL nodes query as a target when you want to target nodes that meet specific conditions. In this case, the orchestrator runs on a list of nodes returned from a PQL nodes query.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

### About this task

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To specify the query on the command line: `puppet job run --query '<QUERY>' <OPTIONS>`
    -   To pass the query in a text file: `puppet job run --query @/path/to/file.txt`
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


## Run Puppet on a list of nodes or a single node

Use a node list target for an orchestrator job when you need to run a job on a specific set of nodes that don't easily resolve to a PQL query. Use a single node or a comma-separated list of nodes.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

### About this task

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To run a job on a single node:

        `puppet job run --nodes <NODE NAME> <OPTIONS>`

    -   To run a job on a list of nodes, use a **comma-separated** list of node names:

        `puppet job run --nodes <NODE NAME>,<NODE NAME>,<NODE NAME>,<NODE NAME> <OPTIONS>`

        **Note:** Do not add spaces in the list of nodes.

    -   To run a job on a node list from a text file:

        `puppet job run --nodes @/path/to/file.txt`

        **Note:** If passing a list of nodes in the text file, put each node on a separate line.


## Run Puppet on a node group

Similar to running Puppet on a list of nodes, you can run it on a node group..

### Before you begin

Make sure you have access to the nodes you want to target.

### About this task

### Procedure

1.  Log into your master or client tools workstation.

2.  Run the command: `puppet job run --node-group <node-group-id>`

    **Tip:** Use the`/v1/groups` endpoint to retrieve a list node groups and their IDs.


**Related information**  


[GET /v1/groups](groups_endpoint.md#)

## Run Puppet on an application or an application instance in an environment

Use applications as a job target to enforce Puppet runs in order on all nodes found in a specific application instance, or to enforce Puppet runs in order on all nodes that are found in each instance of an application.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

### About this task

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To run a job on all instances of an application: `puppet job run --application <APPLICATION> --environment <ENVIRONMENT>`
    -   To run a job on an instance of an application in an environment: `puppet job run --application <APPLICATION INSTANCE> --environment <ENVIRONMENT>`
    **Tip:** You can use `-a` in place of `--application`.


## `puppet job run` command options

The following are common options you can use with the `run` action. For a complete list of global options run `puppet job --help`.

|Option|Value|Description|
|------|-----|-----------|
|`--noop`|Flag, default false|Run a job on all nodes to simulate changes from a new catalog without actually enforcing a new catalog. Cannot be used in conjunction with -`-no-noop` flag.|
|`--no-nopp`|Flag|All nodes run in enforcement mode, and a new catalog is enforced on all nodes. This flag overrides the agent` noop = true` in `puppet.conf`. Cannot be used in conjunction with `--noop `flag.|
|`--environment`, `-e`|Environment name|Overrides the environment specified in the orchestrator configuration file. The orchestrator uses this option to: -   Instruct nodes what environment to run in. If any nodes can’t run in the environment, those node runs will fail. A node will run in an environment if:

    -   The node is included in an application in that environment. These runs may fail if the node is classified into a different environment in the PE node classifier.

    -   The node is classified into that environment in the PE node classifier.

-   Load the application code used to plan run order


|
|`--no-enforce-environment`|Flag, default false|Ignores the environment set by the `--environment`flag for agent runs. When you use this flag, agents run in the environment specified by the PE Node Manager or their `puppet.conf` files.|
|`--description`|Flag, defaults to empty|Provide a description for the job, which will be shown on the job list and job details pages, and returned with the `puppet job show` command.|
|--`concurrency`|Integer|Limits how many nodes can run concurrently. Default is unlimited. You can tune concurrent compile requests in the console.|

## Post-run node status

After Puppet runs, the orchestrator returns a list of nodes and their run statuses.

Node runs can be in progress, completed, skipped, or failed.

-   For a completed node run, the orchestrator prints the configuration version, the transaction ID, a summary of resource events, and a link to the full node run report in the console.
-   For an in progress node run, the orchestrator prints how many seconds ago the run started.

-   For a failed node run, the orchestrator prints an error message indicating why the run failed. In this case, any additional runs will be skipped.


When a run fails, the orchestrator also prints any applications that were affected by the failure, as well as any applications that were affected by skipped node runs.

You can view the status of all running, completed, and failed jobs with the `puppet job show` command, or you can view them from the Job details page in the console.

Additionally, you can use the console to review a list of jobs or to view the details of jobs that have previously run or are in progress. Refer to [Reviewing jobs in the PE console](reviewing_jobs_in_the_console.md#) for more information.

## Stop a job in progress

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

### About this task

When you stop an on-demand Puppet run, runs that are underway will finish, and runs that have not started will not start.

Stopping tasks depends on how many nodes are targeted in the job versus the concurrency limit you've set. If the concurrency limit is higher than the number of nodes targeted, all nodes will complete the task, as those nodes will have already started the task.

### Procedure

1.  To stop a job:

    -   In the console, go to the **Job details** page and select the **Puppet run** or **Task** tab. From the list of jobs, find the one you want and click **Stop**.
    -   On the command line, press **CTRL + C**.

## Viewing job plans

The `puppet job plan` command allows you to preview a plan for a Puppet job without actually enforcing any change. Use this command to ensure your job will run as expected.

You can preview plans for three types of targets, but these targets cannot be combined:

-   View a plan to enforce change on a list of nodes or a single node

-   View a plan to enforce change based on a PQL nodes query

-   View a plan to enforce change on an application or an application instance in an environment


### Results from the job plan command

The `puppet job plan` command returns the following about a job:

-   The environment the job will run in.
-   The target for the job.
-   The maximum concurrency setting for the job.
-   The total number of nodes in the job run.
-   A list of application instances included in the job, if applicable.
-   A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, level 0, have no dependencies. Nodes in level 1 have dependencies in level 0. A node can run once Puppet has finished running on all its dependencies.

**Note:** The `puppet job plan`**does not** generate a job ID. A job ID is only created, and shown in the job plan, when you use `puppet job run`. Use the job ID to view jobs with the puppet job show command.

After you view the plan:

-   Use puppet job run <SAME TARGET AND OPTIONS\> to create and run a job like this.
-   Remember that node catalogs may have changed since this plan was generated.

-   Review and address any errors that the service reports it will encounter when running the job. For example, it will detect any dependency cycles or components that not properly mapped to nodes.


### Example job plan

The following is an example plan summary for a job that will run on a list of nodes that contain a partial application instance.

![](orchestrator_job_plan.png)

### View a job plan for a list of nodes or a single node

You can view a plan for enforcing change on a single node or a comma-separated list of nodes.

#### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

#### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To view a plan for a single node: `puppet job plan --nodes <NODE NAME> <OPTIONS>`
    -   To view a plan for a list of nodes, use a **comma-separated** list of node names. `puppet job plan --nodes <NODE NAME>, <NODE NAME>, <NODE NAME>, <NODE NAME> <OPTIONS>`
    **Tip:** You can use `-n` in place of `--nodes`.


### View a job plan for a PQL nodes query

Use a PQL nodes query as a job target. In this case, the orchestrator presents a plan for a job that could run on a list of nodes returned from a PQL nodes query.

#### Procedure

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


### View a job plan for applications or application instances

You can view a plan for enforcing change on all instances of an application or for a specific instance of an application.

#### Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To view a job plan for all instances of an application: `puppet job plan --application <APPLICATION> <OPTIONS>`
    -   To view a job plan for an application instance in an environment: `puppet job plan --application <APPLICATION INSTANCE> <OPTIONS>`
    **Tip:** You can use `-a` in place of `--application`.


### Puppet job plan command options

The following table shows common options for the `puppet job plan` command. For a complete list of options, run `puppet job --help`.

|Option|Value|Description|
|------|-----|-----------|
|`--environment`, `-e`|Environment name|Overrides the environment specified in the orchestrator configuration file. The orchestrator uses this option to: -   Instruct nodes what environment to run in. If any nodes can’t run in the environment, those node runs will fail. A node will run in an environment if:

    -   The node is included in an application in that environment. These runs may fail if the node is classified into a different environment in the PE node classifier.

    -   The node is classified into that environment in the PE node classifier.

-   Load the application code used to plan run order


|
|--`concurrency`|Integer|Limits how many nodes can run concurrently. Default is unlimited. You can tune concurrent compile requests in the console.|

