---
layout: default
title: "Monitoring Puppet Server metrics"
canonical: "/pe/latest/puppet_server_metrics.html"
---

[Puppet Server](/puppetserver/latest/), which runs on the Puppet master, tracks advanced metrics for the performance and health of the application. There are two ways you can track Puppet Server's performance: using customizable, networked [Graphite and Grafana instances](#getting-started-with-graphite), and using the built-in experimental [developer dashboard](#using-the-developer-dashboard).

> **Note:** Neither of these methods is officially supported. The `grafanadash` module referenced in this document is *not* a Puppet-supported module; it is for testing and demonstration purposes *only*. The developer dashboard is a [tech preview](https://puppetlabs.com/services/tech-preview). Both methods take advantage of [the Status API](./status_api.html), including some endpoints which are also a tech preview.

## Getting started with Graphite

If you don't already have a [Graphite](https://graphite.readthedocs.org/en/latest/overview.html) server and would like to try using a metrics dashboard, you can use a [Puppet module](https://forge.puppetlabs.com/cprice404/grafanadash) that quickly sets up a test Graphite instance. It also configures an instance of [Grafana](http://grafana.org/), a Graphite extension that provides a web UI for creating and displaying metrics dashboards.

Install this module on a dedicated agent **only**, **not** on the Puppet master, as it makes security policy changes that are inappropriate for a Puppet master.

> **Note:** The `grafanadash` module referenced in this document is *not* a Puppet-supported module; it is for testing and demonstration purposes *only*. At the time of this writing, the module supports only CentOS 6. SELinux can cause issues with Graphite and Grafana, so the module temporarily disables SELinux. If you reboot the machine after using the module to install Graphite, you must disable SELinux again and restart the Apache service to use Graphite and Grafana.
>
> The module also disables the `iptables` firewall and enables cross-origin resource sharing on Apache, which are potential security risks.

**To install the Grafana dashboard module:**

Install a Puppet module on a \*nix agent to install and configure the Grafana dashboard.

1. [Install a \*nix PE agent](./install_agents.html) to serve as the Graphite server.
2. As root on the Puppet agent node, run `puppet module install cprice404-grafanadash`.
3. As root on the Puppet agent node, run `puppet apply -e 'include grafanadash::dev'`.

**To run Grafana:**

1. In a web browser on a computer that can reach the Puppet agent node, navigate to `http://<AGENT'S HOSTNAME>:10000`.

    There, you'll see a test screen that indicates whether Grafana can successfully connect to your graphite server. You may see an error message if Grafana is using a hostname that is not resolvable by the machine the browser is running on. Click **view details** and then the **Requests** tab to determine the hostname Grafana is trying to use; if needed, add the IP address and hostname to the `/etc/hosts` or `C:\Windows\system32\drivers\etc\hosts` file on the machine the browser is running on.
2. Download and edit our [sample metrics dashboard](./sample_metrics_dashboard.json).
    a. Open the JSON file in a text editor, on the same computer whose web browser you're using.
    b. Throughout the entire file, replace our sample setting of `master.example.com` with the hostname of your Puppet master. (**Note**: This value MUST be used as the `metrics_server_id` setting, configured below).
    c. Save the file.
3. In the Grafana UI, click **search** (the folder icon), then **Import**, then **Browse**.
4. Navigate to the JSON file you just edited and confirm your choice.

This loads a dashboard with six graphs that display various metrics exported to the Graphite server by Puppet Server. Note that they'll be empty until you enable Graphite metrics, as described below.

### Enabling Puppet Server's Graphite support

Use the "PE Master" node group in the console to configure Puppet Server's [metrics output settings](./puppet_server_config_files.html#metricsconf).

**To enable metrics graphs:**

1. In the console, click **Nodes** in the left navigation bar, then **Classification** in the Node Management bar.
2. On the **Classification** page, select the **PE Master** group.
3. Click the **Classes** tab.
4. Locate the **puppet_enterprise::profile::master** class.
5. Using the **Parameter name** drop-down and **Value** field, set the following parameters to the specified values. (To set a parameter, choose it from the drop-down list, enter the new value, and click **Add parameter**.)
    a. **metrics_graphite_enabled:** set to **true** (**false** is the default value)
    b. **metrics_server_id:** enter the Puppet master hostname.
    c. **metrics_graphite_host:** enter the hostname for the agent node on which you're running Graphite and Grafana.
    d. **metrics_graphite_update_interval_seconds:** enter a value to set Graphite's update frequency in seconds. This setting is optional, and the default value is 5.
6. If you've changed the following settings in the console, ensure they have the following values. Otherwise, leave them on their default values unless your Graphite server uses a non-standard port.
    a. **metrics_enabled:** set to **true** (default value).
    b. **metrics_jmx_enabled:** set to **true** (default value).
    c. **metrics_graphite_port:** set to **2003** (default value) or the Graphite port on your Graphite server.
    d. **profiler_enabled:** set to **true** (default value).
7. Commit the changes.

   Wait for Puppet Server to restart before metrics begin appearing in your dashboard.

> **Tip:** In the Grafana UI, choose the appropriate time window from the drop-down menu.

### Using the sample Grafana dashboard

The sample dashboard provides what we think is an interesting starting point. You can click on the title of any graph, and then click **edit** to tweak the graphs as you see fit.

- **Active requests:** This graph serves as a "health check" for the Puppet server. It shows a flat line that represents the number of CPUs you have in your system, a metric that indicates the total number of HTTP requests actively being processed by the server at any moment in time, and a rolling average of the number of active requests. If the number of requests being processed exceeds the number of CPUs for any significant length of time, your server might be receiving more requests than it can efficiently process.

- **Request durations:** This graph breaks down the average response times for different types of requests made by Puppet agents. This indicates how expensive catalog and report requests are compared to the other types of requests. It also provides a way to see changes in catalog compilation times when you modifyt your Puppet code. A sharp curve upward for all of the types of requests indicates an overloaded server, and they should trend downward after reducing the load on the server.

- **Request ratios:** This graph shows how many requests of each type being handled by the Puppet master. Under normal circumstances, you should see about the same number of catalog, node, or report requests, because these all happen once per agent run. The number of file and file metadata requests correlate to how many remote file resources are in the agents' catalogs.

- **Function calls:** This graph counts how often Puppet manifests call various functions in your catalog compilation. We include a few common functions in the default dashboard, and you can modify this graph to show other functions.

- **Function execution time:** This graph shows the execution time of functions, which suggests which functions are the most computationally expensive --- useful data when looking for ways to optimize your Puppet code. As with the function calls graph, we've included some common default functions, and you can modify this graph to show other functions.

- **Compilation:** This graph breaks catalog compilation down into various phases to show how expensive each phase is on the master.

### Available metrics

The following HTTP and Puppet profiler metrics are available from the Puppet server and can be added to your metrics reporting. Each metric is prefixed with `puppetlabs.<server-id>`; for instance, the Grafana dashboard file refers to the `num-cpus` metric with `puppetlabs.<server-id>.num-cpus`.

#### HTTP metrics

* `http.active-requests.count`: a counter indicating the number of HTTP requests that are currently being processed by the server
* `http.active-histo.mean`: a histogram showing statistics about the number of active HTTP requests over time
* `http.puppet-v3-catalog-.*.-requests.mean` and `http.puppet-v3-catalog-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of Puppet catalog requests processed by the server since startup
* `http.puppet-v3-report-.*.-requests.mean` and `http.puppet-v3-report-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of Puppet report requests processed by the server since startup
* `http.puppet-v3-node-.*.-requests.mean` and `http.puppet-v3-node-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of Puppet node requests processed by the server since startup
* `http.puppet-v3-file_metadatas-.*.-requests.mean` and `http.puppet-v3-file_metadatas-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of requests to the `file_metadatas` endpoint processed by the server since startup
* `http.puppet-v3-file_metadata-.*.-requests.mean` and `http.puppet-v3-file_metadata-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of requests to the `file_metadata` endpoint processed by the server since startup
* `http.puppet-v3-file_content-.*.-requests.mean` and `http.puppet-v3-file_content-.*.-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of requests to the `file_content` endpoint processed by the server since startup
* `http.other-requests.mean` and `http.other-percentage`: tracks the mean number of requests and percentage of total requests, respectively, of requests processed by the server since startup that do not fall into any of the categories above
* `http.total-requests.mean`: tracks the mean number of all requests processed by the server since startup

#### Puppet profiler metrics

* `num-cpus`: the number of CPUs reported by the master
* `functions.count` and `functions.mean`: counts all function calls during catalog compilation and tracks the average execution time of function calls, respectively
* `functions.<function-name>.count` and `functions.<function-name>.mean`: for each function called during catalog compilation, a new metric counts the number of times the function is called (`count`) and tracks the average execution time for that function (`mean`)
* `compiler.compile.<environment>.mean`: tracks the average execution time of all catalog compilations for a specific environment
* `compiler.compile.count` and `compiler.compile.mean`: counts all catalog compilations and tracks the average execution time for all catalog compilations, respectively
* `compiler.compile.<environment>.<node-name>`: counts all catalog compilations and tracks their average execution time, respectively, for optionally specified environment and node
* `compiler.evaluate_ast_node.mean`: tracks the average execution time of the `evaluate_ast_node` phase of catalog compilation
* `compiler.evaluate_main.mean`: tracks the average execution time of the `evaluate_main` phase of catalog compilation
* `compiler.evaluate_node_classes.mean`: tracks the average execution time of the `evaluate_node_classes` phase of catalog compilation
* `compiler.evaluate_definitions.mean`: tracks the average execution time of the `evaluate_definitions` phase of catalog compilation
* `compiler.evaluate_generators.mean`: tracks the average execution time of the `evaluate_generators` phase of catalog compilation
* `compiler.finish_catalog.mean`: tracks the average execution time of the `finish_catalog` phase of catalog compilation
* `compiler.set_node_params.mean`: tracks the average execution time of the `set_node_params` phase of catalog compilation
* `compiler.create_settings_scope.mean`: tracks the average execution time of the `create_settings_scope` phase of catalog compilation

## Using the developer dashboard

While not a customizable as a Graphite dashboard, the developer dashboard is a simple, built-in way to view information about Puppet Server at a glance.

The developer dashboard features metrics particularly relevant to developers of Puppet manifests and modules, which are drawn from the [Status API's metrics endpoints](./status_api.html#metrics-endpoints).

The dashboard charts the current and average number of free and requested JRuby interpreters, as well as the average JRuby borrow and wait times in milliseconds. It also includes lists of the top 10 aggregate API endpoint requests, function calls, and resource declarations by total, mean, and aggregate counts. For more information about these metrics, see the [documentation for the metrics endpoints](./status_api.html#metrics-endpoints).

**To access the developer dashboard:**

Open a web browser and go to `https://<DNS NAME OF YOUR MASTER>:8140/puppet/experimental/dashboard.html`.
