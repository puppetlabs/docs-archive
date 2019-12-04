---
layout: default
title: "Running Puppet on agent nodes"
canonical: "/pe/latest/console_classes_groups_running_puppet.html"
---

## What happens during a Puppet run

Puppet automatically attempts to run on each of your Puppet nodes every 30 minutes. In a Puppet run, the Puppet master and agent nodes perform the following actions:

1. The Puppet agent node sends facts to the Puppet master and requests a catalog.
2. The Puppet master compiles and returns the agent's catalog.
3. The Puppet agent applies the catalog by checking each resource the catalog describes. If it finds any resources that are not in the desired state, it makes the necessary changes.

**Note**: Puppet run behavior differs slightly if [static catalogs](./static_catalogs.html#summarizing-the-differences-in-catalog-behavior) are enabled.

## Options for running Puppet on agent nodes

To kick off a Puppet run outside of the default 30-minute interval, you have several options.

### Run jobs on sets of nodes with the orchestrator

The Puppet orchestrator is a set of interactive tools to control the rollout of configuration changes when and how you want them. You can use the orchestrator from the PE console or from the command line. 

In PE, these on-demand Puppet runs are known as “jobs.”

* Use the PE console to [enforce change on a list of nodes or a single node](./orchestrator_job_run_console.html#run-a-job-on-a-node-list).

  Or use the CLI:
   
  ~~~
  puppet job run --nodes <COMMA-SEPARATED LIST OF NODE NAMES>
  ~~~

* Use the PE console to [enforce change based on a PQL nodes query](./orchestrator_job_run_console.html#run-a-job-on-a-pql-query).

  Or, in the CLI, the following PQL query will run a job on all nodes with a Debian operating system in the production environment:

  ~~~
  puppet job run --query 'nodes[certname] { facts {name = "operatingsystem" and value = "Debian" }}'
  ~~~

* Use the CLI to [enforce change based on an application or application instance](./orchestrator_job_run.html#enforce-change-on-an-application-or-an-application-instance-in-an-environment).

### Run Puppet with the orchestrator API

If you’re putting together your own tools for running Puppet or want to enable CI workflows across your infrastructure, use the orchestrator API.

The [orchestrator API](./orchestrator_api_endpoints.html) consists of a number of endpoints that allow you to control orchestrator jobs and retrieve details about jobs, events, and applications available in your Puppet environments. 

### Run Puppet on a node via SSH

If you want to run Puppet from an agent node, SSH into the node and run `puppet agent --test` (or `puppet agent -t`).

### Run Puppet on a node from the console

1. In the console, click **Inventory**, then select a node.
2. At the top of the screen under the node name , click **Run Puppet**.
3. Select options for the Puppet run, then click **Run**.
   * `noop`: Shows the changes that Puppet would make without actually executing the changes.

      > **Note:** If you set `noop = true` in a node's `puppet.conf` file, then attempt to run Puppet in the console, Puppet runs in noop mode even if you didn't select the `noop` option. This is because the `puppet.conf` file is the source of truth for the noop setting. To run Puppet from the PE console, you must remove the `noop = true` setting from the node's `puppet.conf` file.

   * `debug`: Prints all messages available for use in debugging.

   * `trace`: Prints stack traces on some errors.

   * `evaltrace`: Shows a breakdown of the time taken for each step in the Puppet run.
   
   When the Puppet run is complete, the console will display the node's [run status](./CM_overview.html#how-puppet-determines-node-run-statuses).

#### Why can't I run Puppet in the console?

The **Run Puppet** button is enabled if the node is currently connected to the Puppet communications protocol (PCP) broker. If an agent does not have an active websocket session with the PCP broker, the **Run Puppet** button is disabled.

>**Note**: To use the **Run Puppet** button on an agent, your agent must be running PE 2015.3 or later.

## Activity logging

When you initiate a Puppet run using the **Run Puppet** button, the Activity service logs the activity. In the PE console, you can view activity on a single node by selecting the node, then clicking the **Activity** tab.

Alternatively, you can use the Activity Service API to retrieve activity information. For more information about forming HTTP requests to the Activity Service API, see the [Activity Service API docs](./rbac_activityapis.html).

>**Note:** activity logging is not yet available for Puppet runs created wth the orchestration service.

### To get all entries for all nodes:

From the CLI on the Puppet master:

~~~~
    curl -X GET -H 'Content-Type: application/json'
      --cert $(puppet config print hostcert)
      --key $(puppet config print hostprivkey)
      --cacert $(puppet config print localcacert)
      "https://<HOSTNAME OF MASTER>:4433/activity-api/v1/events?service_id=pe-console"
~~~~

### To get all entries for a single node:

From the CLI on the Puppet master:

~~~~
    curl -X GET -H 'Content-Type: application/json'
      --cert $(puppet config print hostcert)
      --key $(puppet config print hostprivkey)
      --cacert $(puppet config print localcacert)
      "https://<HOSTNAME OF MASTER>:4433/activity-api/v1/events?service_id=pe-console&object_type=node&object_id=<hostname>"
~~~~

The response is structured like this:

~~~~
    {
      "commits": [
        {
          "events": [
            {
              "message": "Start `puppet agent --no-use_cached_catalog --ignorecache --pluginsync --show_diff --no-usecacheonfailure --onetime --no-splay --no-daemonize --verbose` on puppetmaster.example.com"
            }
          ],
          "object": {
            "id": "puppetmaster.example.com",
            "name": "puppetmaster.example.com"
          },
          "subject": {
            "id": "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
            "name": "admin"
          },
          "timestamp": "2015-10-23T21:12:51Z"
        },
        {
          "events": [
            {
              "message": "Request `puppet agent --no-use_cached_catalog --ignorecache --pluginsync --show_diff --no-usecacheonfailure --onetime --no-splay --no-daemonize --verbose` on puppetmaster.example.com"
            }
          ],
          "object": {
            "id": "puppetmaster.example.com",
            "name": "puppetmaster.example.com"
          },
          "subject": {
            "id": "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
            "name": "admin"
          },
          "timestamp": "2015-10-23T21:12:51Z"
        }
      ],
      "offset": 0,
      "limit": 20,
      "total-rows": 2
    }
~~~~

## Troubleshooting Puppet run failures

The table below shows some errors that can occur when you attempt to run Puppet, and some suggestions for troubleshooting them. This is by no means an exhaustive list of causes, just some ideas to get you started.

To learn more about why your Puppet run failed, you can view the events and log for the Puppet run. If Puppet can generate a report, it creates a **View Report** link when the Puppet run fails. Click the link to access the **Events** and **Log** tabs.

| Error        | Possible Cause |
|---           |---             |
|Changes could not be applied  | Conflicting classes are a common cause. Check the log to get more detail.|
|Noop, changes could not be applied   | Conflicting classes are a common cause. Check the log to get more detail. |
|Run already in progress  | Occurs when a run is triggered in the CLI or by another user, and you click **Run**. |
|Run request times out   | Occurs if you click **Run** and the Puppet agent isn't available. |
|Report request times out  | Occurs when the report is not successfully stored in PuppetDB after the run completes.|
|Invalid response (such as a 500 error)   | Your Puppet code might be have incorrect formatting. |
|Button is disabled and a run is not allowed.  | The user has permission, but the Puppet agent is not responding. |


