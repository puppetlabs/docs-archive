---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running Puppet on demand from the CLI

Use the **puppet job run** command to enforce change on your agent nodes with on-demand Puppet runs.

Use the **puppet job** tool to enforce change across nodes. For example, when you add a new class parameter to a set of nodes or deploy code to a new Puppet environment, use this command to run Puppet across all the nodes in that environment.

If you run a job on a node that has relationships outside of the target \(for example, it participates in an application that includes nodes not in the job target\) the job still runs only on the node in the target you specified. In such cases, the orchestrator notifies you that external relationships exist. It prints the node with relationships, and it prints the applications that might be affected. For example:

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


## Run Puppet on a PQL query

Use a PQL nodes query as a target when you want to target nodes that meet specific conditions. In this case, the orchestrator runs on a list of nodes returned from a PQL nodes query.

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

Make sure you have permissions to run tasks and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

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


To view the status of the Puppet job, run `puppet job show <job ID>`. To view a list of the previous 50 running and complete Puppet jobs, run `puppet job show`

## Run Puppet on a list of nodes or a single node

Use a node list target for an orchestrator job when you need to run a job on a specific set of nodes that don't easily resolve to a PQL query. Use a single node or a comma-separated list of nodes.

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

Make sure you have permissions to run tasks and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To run a job on a single node:

        `puppet job run --nodes <NODE NAME> <OPTIONS>`

    -   To run a job on a list of nodes, use a **comma-separated** list of node names:

        `puppet job run --nodes <NODE NAME>,<NODE NAME>,<NODE NAME>,<NODE NAME> <OPTIONS>`

        **Note:** Do not add spaces in the list of nodes.

    -   To run a job on a node list from a text file:

        `puppet job run --nodes @/path/to/file.txt`

        **Note:** If passing a list of nodes in the text file, put each node on a separate line.


To view the status of the Puppet job, run `puppet job show <job ID>`. To view a list of the previous 50 running and complete Puppet jobs, run `puppet job show`

## Run Puppet on a node group

Similar to running Puppet on a list of nodes, you can run it on a node group..

Make sure you have access to the nodes you want to target.

1.  Log into your master or client tools workstation.

2.  Run the command: `puppet job run --node-group <node-group-id>`

    **Tip:** Use the`/v1/groups` endpoint to retrieve a list node groups and their IDs.


**Related information**  


[GET /v1/groups](groups_endpoint.md#)

## Run Puppet on an application or an application instance in an environment

Use applications as a job target to enforce Puppet runs in order on all nodes found in a specific application instance, or to enforce Puppet runs in order on all nodes that are found in each instance of an application.

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

Make sure you have permissions to run tasks and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To run a job on all instances of an application: `puppet job run --application <APPLICATION> --environment <ENVIRONMENT>`
    -   To run a job on an instance of an application in an environment: `puppet job run --application <APPLICATION INSTANCE> --environment <ENVIRONMENT>`
    **Tip:** You can use `-a` in place of `--application`.


To view the status of the Puppet job, run `puppet job show <job ID>`. To view a list of the previous 50 running and complete Puppet jobs, run `puppet job show`

## `puppet job run` command options

The following are common options you can use with the `run` action. For a complete list of global options run `puppet job --help`.

|Option|Value|Description|
|------|-----|-----------|
|`--noop`|Flag, default false|Run a job on all nodes to simulate changes from a new catalog without actually enforcing a new catalog. Cannot be used in conjunction with -`-no-noop` flag.|
|`--no-nopp`|Flag|All nodes run in enforcement mode, and a new catalog is enforced on all nodes. This flag overrides the agent` noop = true` in `puppet.conf`. Cannot be used in conjunction with `--noop `flag.|
|`--environment`, `-e`|Environment name|Overrides the environment specified in the orchestrator configuration file. The orchestrator uses this option to: -   Instruct nodes what environment to run in. If any nodes can’t run in the environment, those node runs fail. A node runs in an environment if:

-   The node is included in an application in that environment. These runs may fail if the node is classified into a different environment in the PE node classifier.

-   The node is classified into that environment in the PE node classifier.

-   Load the application code used to plan run order


|
|`--no-enforce-environment`|Flag, default false|Ignores the environment set by the `--environment`flag for agent runs. When you use this flag, agents run in the environment specified by the PE Node Manager or their `puppet.conf` files.|
|`--description`|Flag, defaults to empty|Provide a description for the job, to be shown on the job list and job details pages, and returned with the `puppet job show` command.|
|--`concurrency`|Integer|Limits how many nodes can run concurrently. Default is unlimited. You can tune concurrent compile requests in the console.|

## Post-run node status

After Puppet runs, the orchestrator returns a list of nodes and their run statuses.

Node runs can be in progress, completed, skipped, or failed.

-   For a completed node run, the orchestrator prints the configuration version, the transaction ID, a summary of resource events, and a link to the full node run report in the console.
-   For an in progress node run, the orchestrator prints how many seconds ago the run started.

-   For a failed node run, the orchestrator prints an error message indicating why the run failed. In this case, any additional runs are skipped.


When a run fails, the orchestrator also prints any applications that were affected by the failure, as well as any applications that were affected by skipped node runs.

You can view the status of all running, completed, and failed jobs with the `puppet job show` command, or you can view them from the Job details page in the console.

Additionally, on the **Jobs** page in the console, you can review a list of jobs or to view the details of jobs that have previously run or are in progress.

## Stop a job in progress

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

When you stop an on-demand Puppet run, runs that are underway finish, and runs that have not started are canceled.

1.  To stop a job:

    -   In the console, go to the **Jobs** page and select the **Puppet run** tab. From the list of jobs, find the one you want and click **Stop**.
    -   On the command line, press **CTRL + C**.

