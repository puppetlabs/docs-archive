---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Bolt Plan example

View a plan that combines multiple tasks with one command.

## Plan that deploys an application 

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

