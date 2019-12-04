# Reviewing jobs on the CLI

Use the `puppet job show` command to view running or completed orchestration jobs.

## Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run.`

## About this task

Additionally, you can view a list of jobs or view the details of jobs that have previously run or are in progress from the Job details page in the PE console.

## Procedure

1.  Log into your Puppet master or client tools workstation and run one of the following commands:

    -   To check the status of a running or completed job, use the following command: `puppet job show <job ID>`

        The command returns the following:

        -   The status of the job \(running, completed, or failed\).
        -   The start \(and finish\) time.
        -   The elapsed time or duration of the run.
        -   The user who ran the job.
        -   The environment the job ran in if you set the environment.
        -   The target specified for the job.
        -   Whether no-op mode was enforced.
        -   The number of nodes in the job.
        -   The job description, if set.
    -   To view a list of running and completed jobs, up to 50 maximum \(the concurrency limit\), ordered by timestamp, use the following command: `puppet job show`

## Viewing jobs triggered without the `puppet job` command

If you run any orchestrator jobs through the console or the orchestrator API , the `puppet job show` command will also print those jobs.

You can use the `puppet job show` command with or without a job ID.

```
puppet job show

ID  STATUS    TIMESTAMP       USER            NODES      TARGET
------------------------------------------------------------------
238 Running   Jul-02 9:15:15   <API user>      8          query
239 Running   Jul-02 9:15:22   <API user>      3          ??
240 Running   Jul-02 9:15:15   <Job run user>  100        all
   
*environment not enforced on a per node basis
```

### The enforce\_environment option

The `enforce_environment` option is an option on the `/command/` API endpoint. By default `enforce_environment` is set to `true`, which means agent nodes are forced to run in the same environment in which their configured applications are defined. If set to false, agent nodes will run in whatever environment they are classified in or assigned to. API jobs may not have an environment specified. If a job has no environment specified, agent node run ordering **is not** specified by applications.

