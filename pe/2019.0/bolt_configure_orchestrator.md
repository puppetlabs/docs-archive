---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Using Bolt with orchestrator

Bolt enables running a series of tasks — called plans — to help you automate the manual work of maintaining your infrastructure. When you pair Bolt with PE, you get advanced automation with the management and logging capabilities of PE.

Bolt connects directly to remote nodes with SSH or WinRM, so you are not required to install any agent software. To learn more about Bolt, see the [Bolt documentation](https://puppet.com/docs/bolt/latest/bolt.html).

You can configure Bolt to use the orchestrator API and perform actions on PE nodes. When you run Bolt plans, the plan logic is processed locally on the node running Bolt while corresponding commands, scripts, tasks, and file uploads run remotely using the orchestrator API.

Before you can use Bolt with PE, you must [install Bolt](https://puppet.com/docs/bolt/latest/bolt_installing.html).

To set up Bolt to use the orchestrator API, you must:

-   Install the `bolt_shim` module in a PE environment.

-   Assign task permissions to a user role.

-   Adjust the orchestrator configuration files, as needed.


## Install the Bolt module in a PE environment

Bolt uses a task to execute commands, upload files, and run scripts over orchestrator. To install this task, install the [`puppetlabs-bolt_shim` module](https://forge.puppet.com/puppetlabs/bolt_shim) from the Forge. Install the code in the same environment as the other tasks you want to run. Use the following Puppetfile line:

```
mod 'puppetlabs-bolt_shim', '0.3.0'
```

In addition to the `bolt_shim` module, any task or module content you want to execute over Puppet Communications Protocol \(PCP\) must be present in the PE environment. For details about downloading and installing modules for Bolt, see [Set up Bolt to download and install modules](https://puppet.com/docs/bolt/latest/installing_tasks_from_the_forge.html#task-8928). By allowing only content that is present in the PE environment to be executed over PCP, you maintain role-based access control over the nodes you manage in PE.

To enable the Bolt `apply` action, you must install the [`puppetlabs-apply_helpers` module](https://forge.puppet.com/puppetlabs/apply_helpers). Use the following Puppetfile line:

```
mod 'puppetlabs-apply_helpers', '0.1.0'
```

**Note:** Bolt over orchestrator can require a large amount of memory to convey large messages, such as the plugins and catalogs sent by `apply`. You might need to [increase the Java heap size](https://puppet.com/docs/pe/latest/config_java_args.html#increase-the-java-heap-size-for-pe-java-services) for orchestration services.

## Assign task permissions to a user role

CAUTION:

By granting users access to Bolt tasks, you give them permission to run arbitrary commands and upload files as a super-user.

1.  In the console, click **Access control** \> **User roles**.

2.  From the list of user roles, click the role you want to have task permissions.

3.  On the **Permissions** tab, in the **Type** box, select **Tasks**.

4.  For **Permission**, select **Run tasks**, and select **All** from the **Instance** drop-down list.

5.  Click **Add permission**, and commit the change.


## Adjust the orchestrator configuration files

Set up the orchestrator API for Bolt in the same configuration file that is used for PE client tools:

-   **\*nix** `/etc/puppetlabs/client-tools/orchestrator.conf`

-   **Windows** `C:/ProgramData/PuppetLabs/client-tools/orchestrator.conf`


**Note:** If you use a global configuration file stored at `/etc/puppetlabs/client-tools/orchestrator.conf` \(or `C:\ProgramData\PuppetLabs\client-tools\orchestrator.conf` for Windows\), copy the file to your home directory.

Alternatively, you can configure Bolt to connect to orchestrator in the `pcp` section of the Bolt configuration file. This configuration is not shared with `puppet task`. By default, Bolt uses the production environment in PE when running tasks. To use a different environment, change the `task-environment` setting:

```
pcp:
  task-environment: development
```

## Specify the transport

Bolt runs tasks through the orchestrator when a target uses the `pcp` transport. Specify the transport for specific nodes by using the PCP protocol in the target's URI, like `pcp://puppet.certname`, or setting `transport` in a `config` section in `inventory.yaml`. Change the default transport for all nodes by setting `transport` in `bolt.yaml` or passing `--transport pcp` on the command line.

## View available tasks

To view a list of available tasks from the orchestrator API, run the command `puppet task show` \(instead of the command `bolt task show`\).

## Bolt Plan example

View a plan that combines multiple tasks with one command.

### Plan that deploys an application 

In this example, the plan my\_app runs the tasks necessary to deploy an application to multiple nodes. It uses node information from an inventory file and tasks written in Python and stored at `my_app/tasks/`.

You run the plan with this command:

```
bolt plan run my_app::deploy version=1.0.2 app_servers=app db_server=db lb_server=lb --inventoryfile ./inventory.yaml --modulepath=./modules
```

Using the sample code below, the plan validates that there is a single load balancer server; queries the server load to determine availability, installs the application, migrates the database, makes the new code available on each application server and, finally, cleans up old versions of the application.

**Note:** This is sample code. To set up these tasks and run this plan in your environment, try the Puppet Tasks Hands-on Lab. This GitHub repository contains sample files, code examples, and exercises to help you interact with Bolt in a safe environment. For more information, see the [puppetlabs/tasks-hands-on-lab repository](https://github.com/puppetlabs/tasks-hands-on-lab#puppet-tasks-hands-on-lab).

```
plan my_app::deploy(
  Pattern[/\d+\.\d+\.\d+/] $version,
  TargetSpec $app_servers,
  TargetSpec $db_server,
  TargetSpec $lb_server,
  String[1] $instance = 'my_app',
  Boolean $force = false
) {
  # Validate that there is only a single load balancer server to check
  if get_targets($lb_server).length > 1 {
    fail_plan("${lb_server} did not resolve to a single target")
  }

  # First query the load balancer and make sure the app isn't under too much load to do a deploy.
  unless $force {
    $conns = run_task('my_app::lb', $lb_server,
       "Check load before starting deploy",
       action => 'stats',
       backend => $instance,
       server => 'FRONTEND',
    ).first['connections']
    if ($conns > 8) {
      fail_plan("The application has too many open connections: ${conns}")
    } else {
      # Info messages are displayed when the --verbose flag is used.
      info("Application has ${conns} open connections.")
    }
  }

  # Install the new version of the application and check what version was previously
  # installed so it can be deleted after the deploy.
  $old_versions = run_task('my_app::install', [$app_servers, $db_server],
    "Install ${version} of the application",
    version => $version
  ).map |$r| { $r['previous_version'] }

  run_task('my_app::migrate', $db_server)

  # Don't log every action on each node, only log important messages
  without_default_logging() || {
    # Expand group references or globs before iterating
    get_targets($app_servers).each |$server| {

      # Check stats and print a message to the user
      $stats = run_task('my_app::lb', $lb_server,
        action => 'stats',
        backend => $instance,
        server => $server.name,
        _catch_errors => $force
      ).first
      notice("Deploying to ${server.name}, currently ${stats["status"]} with ${stats["connections"]} open connections.")

      run_task('my_app::lb', $lb_server,
        "Drain connections from ${server.name}",
        action => 'drain',
        backend => $instance,
        server => $server.name,
        _catch_errors => $force
      )

      run_task('my_app::deploy', [$server],
        "Update application for new version",
      )

      # Verify the app server is healthy before returning it to the load
      # balancer.
      $health = run_task('my_app::health_check', $lb_server,
        "Run Healthcheck for ${server.name}",
        target => "http://${server.name}:5000/",
        '_catch_errors' => true).first

      if $health['status'] == 'success' {
        info("Upgrade Healthy, Returning ${server.name} to load balancer")
      } else {
        # Fail the plan unless the app server is healthy or this is a forced deploy
        unless $force {
          fail_plan("Deploy failed on app server ${server.name}: ${health.result}")
        }
      }

      run_task('my_app::lb', $lb_server,
        action => 'add',
        backend => $instance,
        server => $server.name,
        _catch_errors => $force
      )
      notice("Deploy complete on ${server}.")
    }
  }

  run_task('my_app::uninstall', [$db_server, $app_servers],
    "Clean up old versions",
    live_versions => $old_versions + $version,
  )
}
```

**Related information**  


[Puppet Tasks Hands-on Lab](https://github.com/puppetlabs/tasks-hands-on-lab#puppet-tasks-hands-on-lab)

