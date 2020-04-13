# Review jobs from the command line

Use the `puppet job show` command to view running or completed jobs.

## Before you begin

The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](configuring_puppet_orchestrator.md#) for information about setting RBAC permissions and token authorization.

If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-task run`.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

Make sure you have permissions to run tasks and PQL queries.

Make sure you have permissions to run jobs and PQL queries.

## About this task

Additionally, you can view a list of jobs or view the details of jobs that have previously run or are in progress from the Job details page in the console.

## Procedure

1.  Log into your Puppet master or client tools workstation.

2.  Run one of the following commands:

    -   To check the status of a running or completed job, run: `puppet job show <job ID>`

        The command returns the following:

        -   The status of the job \(running, finished, or failed\).
        -   The job type: Puppet run, task, or plan task \(a plan combines multiple tasks and runs them with a single Bolt command\). 
        -   The job description, if set.
        -   The start and finish time.
        -   The elapsed time or duration of the run.
        -   The user who ran the job.
        -   The environment the job ran in, if you set the environment.
        -   Whether no-op mode was enforced.
        -   The number of nodes in the job.
        -   The target specified for the job.
    -   To view a list of running and completed jobs, up to 50 maximum \(the concurrency limit\), ordered by timestamp, run: `puppet job show`

## Viewing jobs triggered without the `puppet job` command

When you run any orchestrator jobs through the console or the orchestrator API, the `puppet job show` command also prints those jobs.

You can use the `puppet job show` command with or without a job ID.

```
puppet job show

ID    STATUS    JOB TYPE                      TIMESTAMP               TARGET
------------------------------------------------------------------------------------------------------
3435  failed    echo                          04/04/2018 06:07:18 PM  192-168-2-171.rfc... 3434  finished  puppet run                    04/04/2018 05:57:27 PM  911bcc40-4550-44e...
3433  finished  package                       04/04/2018 05:53:14 PM  911bcc40-4550-44e...
3432  finished  echo                          04/04/2018 05:38:36 PM  bronze-10,bronze-...
   
*environment not enforced on a per node basis
```

### The enforce\_environment option

The `enforce_environment` option is an option on the `/command/` API endpoint. By default `enforce_environment` is set to `true`, which means agent nodes are forced to run in the same environment in which their configured applications are defined. If set to false, agent nodes run in whatever environment they are classified in or assigned to. API jobs might not have an environment specified. If a job has no environment specified, agent node run ordering **is not** specified by applications.

