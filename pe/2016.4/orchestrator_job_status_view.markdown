---
layout: default
title: "Reviewing Puppet jobs"
canonical: "/pe/latest/orchestrator_job_status_view.html"
---

The `puppet job show` command allows you to view running or completed jobs.

## View running or completed jobs

Use the `puppet job show` command to view running or completed orchestration jobs.

>**Note**: The first time you run a command, you need to authenticate. See the[orchestrator installation instructions](./orchestrator_install.html#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

>**Note:** If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-job show`.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To check the status of a running or completed job, use the following command:
   
     ~~~
     puppet job show <job ID>
     ~~~

     The command returns the following:

     - The status of the job (running, completed, or failed). See [Post-run node statuses](./orchestrator_job_run.html#post-run-node-status) for details.
     - The start (and finish) time.
     - The elapsed time or duration of the run.
     - The user who ran the job.
     - The environment the job ran in, or if the environment was not specified.
     - The target specified for the job.
     - Whether no-op mode was enforced.
   
   - To view a list of running and completed jobs, up to 50 maximum (the concurrency limit), ordered by timestamp, use the following command:
   
     ~~~
     puppet job show  
     ~~~

## Command options

The following are common options you may use with the `show` action. For a complete list of global options run `puppet job --help`.

<table>
  <tr>
   <th> Option </th>
   <th> Value </th>
   <th> Description </th>
  </tr>
 <tr>
   <td width="175"> <code> --environment </code>, <code> -e </code> </td>
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
   <td width="175"> <code> --limit </code> </td>
   <td width="175"> Integer </td>
   <td> Limit the number of printed jobs printed (when no job-id is specified). </td>
 </tr>
</table>

Run the command without any options to return a list of all jobs in all environments (up to 50 jobs maximum).

The command returns something like the following:

~~~
ID  STATUS    TIMESTAMP       USER            ENVIRONMENT   NODES      TARGET
---------------------------------------------------------------------------------------
234 Running   Jul-02 9:15:15   <Job run user>  production     100         whole environment
236 Complete  Jul-01 10:45:22  <Job run user>  dev             3          ExampleApp::FullAppStack
237 Complete  Jul-01 10:45:22  <Job run user>  production      6          ExampleApp['example\_app_instance']
~~~

## Viewing jobs triggered without the `puppet job` command

If you run any orchestrator jobs through the API or the PE console's **Run puppet** button, the `puppet job show` command will also print these jobs. 

You can use the `puppet job show` command with or without a job ID. 

~~~
puppet job show

ID  STATUS    TIMESTAMP       USER            ENVIRONMENT   NODES      TARGET
---------------------------------------------------------------------------------------
238 Running   Jul-02 9:15:15   <API user>      dev*            8          query
239 Running   Jul-02 9:15:22   <API user>      n/s             3          ??
240 Running   Jul-02 9:15:15   <Job run user>  production     100         all
   
*environment not enforced on a per node basis
~~~

### The `enforce_environment` option

The `enforce_environment` option is an option on the `/command/` API endpoint. By default `enforce_environment` is set to `true`, which means agent nodes are forced to run in the same environment in which their configured applications are defined. If set to false, agent nodes will run in whatever environment they are classified in or assigned to. API jobs may not have an environment specified. If a job has no environment specified, agent node run ordering **is not* specified by applications.   
