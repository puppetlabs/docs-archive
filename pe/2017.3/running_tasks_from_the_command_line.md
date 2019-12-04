---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks from the command line

Use the `puppet task run` command to run tasks on agent nodes. For example, do you want to inspect a package or quickly stop a particular service? Use the `puppet task` tool and the relevant module to make those changes arbitrarily, rather than through a Puppet configuration change.

## Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run.`

## About this task

You can run tasks on a single node, on nodes identified in a static list, or on nodes retrieved by a PQL query.

Use the orchestrator command `puppet task` to trigger task runs.

## Results

## Run a task on a PQL query

For some tasks, you may want to target nodes that meet specific conditions. For such tasks, create a PQL query.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run.`

### About this task

Make sure you have installed any tasks you want to use.

You must have permissions for running tasks and PQL queries.

### Procedure

1.  Log into your master or client tools workstation and run one of the following commands:

    -   To specify the query on the command line: `puppet task run <TASK NAME> <PARAMETER>=<VALUE>, <PARAMETER>=<VALUE> --query '<QUERY>' <OPTIONS>`
    -   To pass the query in a text file: `puppet task run <TASK NAME> <PARAMETER>=<VALUE>, <PARAMETER>=<VALUE> --query @/path/to/file.txt`

        For example, to run the service task with two required parameters: `puppet-task run service action=status service=nginx --nodes host1,host2,host3`

    **Tip:** Use `puppet task show <TASK NAME>` to see a list of available parameters for a task. Not all tasks require parameters.

    Refer to the `puppet task` command options to see how to pass parameters with the \`--params\` flag.

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

    **Tip:** You can use `-q` in place of `--query`.


## Run a task on a list of nodes or a single node

Use a node list target when you need to run a job on a set of nodes that doesn't easily resolve to a PQL query. Use a single node or a comma-separated list of nodes.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run.`

### About this task

Make sure you have installed any tasks you want to use.

You must have permissions for running tasks and PQL queries.

### Procedure

1.  Log into your master or client tools workstation and run one of the following commands:

    -   To run a task job on a single node: `puppet task run <TASK NAME> <PARAMETER>=<VALUE>, <PARAMETER>=<VALUE> --nodes <NODE NAME> <OPTIONS>`
    -   To run a task job on a list of nodes, use a comma-separated list of node names: `puppet task run <TASK NAME> <PARAMETER>=<VALUE>, <PARAMETER>=<VALUE> --nodes <NODE NAME>,<NODE NAME>,<NODE NAME>,<NODE NAME> <OPTIONS>`

        **Note:** Do not add spaces in the list of nodes.

    -   To run a task job on a node list from a text file: `puppet task run <TASK NAME> <PARAMETER>=<VALUE>, <PARAMETER>=<VALUE> --nodes @/path/to/file.txt`

        **Note:** In the text file, put each node on a separate line.

    **Tip:** Use `puppet task show <TASK NAME>` to see a list of available parameters for a task. Not all tasks require parameters.

    Refer to the `puppet task` command options to see how to pass parameters with the \`--params\` flag.


## `puppet task run` command options

The following are common options you may use with the `task` action. For a complete list of global options run `puppet task --help`.

|Option|Value|Description|
|------|-----|-----------|
|`--noop`|Flag, default false|Run a task to simulate changes without actually enforcing the changes.|
|``--params``|String|Specify a JSON object that includes the parameters, or specify the path to a JSON file containing the parameters, prefaced with `@`, for example, `@/path/to/file.json`. **Do not use this flag if specifying parameter-value pairs in the command.**|
|``--environment`, `-e``|Environment name|Use tasks installed in the specified environment.|
|```--description```|Flag, defaults to empty|Provide a description for the job, which will be shown on the job list and job details pages, and returned with the `puppet job show` command.|

## Reviewing task job output

The output the orchestrator returns depends on the type of task you run. Output will either be standard output \(STDOUT\) or structured output. At minimum, the orchestrator prints a new job ID and the number of nodes in the task.

The following example shows a task to check the status of the Puppet service running on a list of nodes derived from a PQL query.

```
[example@orch-master ~]$ puppet task run service service=puppet action=status -q 'nodes {certname ~ "br"}' --environment=production
Starting job ...
New job ID: 2029
Nodes: 8

Started on bronze-11 ...
Started on bronze-8 ...
Started on bronze-3 ...
Started on bronze-6 ...
Started on bronze-2 ...
Started on bronze-5 ...
Started on bronze-7 ...
Started on bronze-10 ...
Finished on node bronze-11
  status : running
  enabled : true
Finished on node bronze-3
  status : running
  enabled : true
Finished on node bronze-8
  status : running
  enabled : true
Finished on node bronze-7
  status : running
  enabled : true
Finished on node bronze-2
  status : running
  enabled : true
Finished on node bronze-6
  status : running
  enabled : true
Finished on node bronze-5
  status : running
  enabled : true
Finished on node bronze-10
  status : running
  enabled : true

Job completed. 8/8 nodes succeeded.
Duration: 1 sec
	 
```

View the status of all running, completed, and failed jobs with the `puppet job show` command, or view them from the Job details page in the console.

Additionally, use the console to review a list of jobs or to view the details of jobs that have previously run or are in progress.

## Inspect tasks

The **puppet task show** command shows you tasks that you have installed, and documentation for those tasks.

### Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run.`

### About this task

Make sure you have installed any tasks you want to use.

You must have permissions for running tasks and PQL queries.

### Procedure

1.  Log into your master or client tools workstation and run one of the following commands:

    -   To check the documentation for a specific task: `puppet task show <TASK>`. The command returns the following:
        -   The command format for running the task
        -   Any parameters available to use with the task
    -   To view a list of all available tasks: `puppet task show`

## Stop a job

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

### About this task

When you stop an on-demand Puppet run, runs that are underway will finish, and runs that have not started will not start.

Stopping tasks depends on how many nodes are targeted in the job versus the concurrency limit you've set. If the concurrency limit is higher than the number of nodes targeted, all nodes will complete the task, as those nodes will have already started the task.

### Procedure

1.  To stop a job:

    -   In the console, navigate to the **Job details** page for the job you want to stop, and click **Stop**. Or,
    -   On the command line, press **CTRL + C**.

