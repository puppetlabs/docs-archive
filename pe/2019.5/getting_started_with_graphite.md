# Getting started with Graphite

Puppet Enterprise can export many metrics to Graphite, a third-party monitoring application that stores real-time metrics and provides customizable ways to view them. After Graphite support is enabled, Puppet Server exports a set of metrics by default that is designed to be immediately useful to Puppet administrators.

**Note:** A Graphite setup is deeply customizable and can report many different Puppet Server metrics on demand. However, it requires considerable configuration and additional server resources. For an easier, but more limited, web-based dashboard of Puppet Server metrics built into Puppet Server, use the developer dashboard. To retrieve metrics manually via HTTP, use the Status API.

To use Graphite with Puppet Enterprise, you must:

-   Install and configure a Graphite server.
-   Enable Puppet Server's Graphite support

Grafana provides a web-based customizable dashboard that's compatible with Graphite, and the Grafanadash module installs and configures it by default.

## Using the Grafanadash module

The Grafanadash module quickly installs and configures a basic test instance of Graphite with the Grafana extension. When installed on a dedicated Puppet agent, this module provides a quick demonstration of how Graphite and Grafana can consume and display Puppet Server metrics.

CAUTION:

The Grafanadash module referenced in this document is not a Puppet-supported module; it is for testing and demonstration purposes only. It is tested against CentOS 7 only. Also, install this module on a dedicated agent only. Do not install it on the Puppet master, because the module makes security policy changes that are inappropriate for a Puppet master:

-   SELinux can cause issues with Graphite and Grafana, so the module temporarily disables SELinux. If you reboot the machine after using the module to install Graphite, you must disable SELinux again and restart the Apache service to use Graphite and Grafana.

-   The module disables the iptables firewall and enables cross-origin resource sharing on Apache, which are potential security risks.


### Installing the Grafanadash module

Install the Grafanadash module on a \*nix agent. The module's `grafanadash::dev` class installs and configures a Graphite server, the Grafana extension, and a default dashboard.

#### Procedure

1.  Install a \*nix PE agent to serve as the Graphite server.

2.  As root on the Puppet agent node, run `puppet module install puppetlabs-grafanadash`.

3.  As root on the Puppet agent node, run `puppet apply -e 'include grafanadash::dev'`.


### Running Grafana

Grafana is a dashboard that can interpret and visualize Puppet Server metrics over time, but you must configure it to do so.

#### About this task

Grafana runs as a web dashboard, and the Grafanadash module configures it at port 10000 by default. However, there are no Puppet metrics displayed by default. You must create a metrics dashboard to view Puppet's metrics in Grafana, or edit and import a JSON-based dashboard such as the sample Grafana dashboard that we provide.

#### Procedure

1.  In a web browser on a computer that can reach the Puppet agent node, navigate to `http://<AGENT'S HOSTNAME>:10000`.

2.  Open the `sample_metrics_dashboard.json` file in a text editor on the same computer you're using to access Grafana.

3.  Throughout the file, replace our sample setting of `master.example.com` with the hostname of your Puppet master. This value must be used as the `metrics_server_id` setting, as configured below.

4.  Save the file.

5.  In the Grafana UI, click **search** \(the folder icon\), then **Import**, then **Browse.**

6.  Navigate to and select the edited JSON file.


#### Results

This loads a dashboard with nine graphs that display various metrics exported from the Puppet Server to the Graphite server. However, these graphs remain empty until you enable Puppet Server's Graphite metrics.

**Related information**  


[Sample Grafana dashboard graphs](getting_started_with_graphite.md#)

## Enabling Puppet Server's Graphite support

Use the PE Master node group in the console to configure Puppet Server's metrics output settings.

### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2.  On the **Configuration** tab, in the `puppet_enterprise::profile::master` class, add these parameters:

    1.  Set `metrics_graphite_enabled` to `true` \(default is false\).

    2.  Set `metrics_server_id` to the Puppet master hostname.

    3.  Set `metrics_graphite_host` to the hostname for the agent node on which you're running Graphite and Grafana.

    4.  Set `metrics_graphite_update_interval_seconds` to a value to set Graphite's update frequency in seconds. This setting is optional, and the default value is `60`.

3.  Verify that these parameters are set to their default values, unless your Graphite server uses a non-standard port:

    1.  Set `metrics_jmx_enabled` to `true` \(default value\).

    2.  Set `metrics_graphite_port` to `2003` \(default value\) or the Graphite port on your Graphite server.

    3.  Set `profiler_enabled` to `true` \(default value\).

4.  Commit changes.


### What to do next

**Note:** In the Grafana UI, choose an appropriate time window from the drop-down menu.

**Note:** The `puppet_enterprise::profile::master::metrics_enabled` parameter used in Puppet Enterprise 2016.3 and earlier is no longer necessary and has been deprecated. If you set it, PE notifies you of the setting's deprecation.

## Sample Grafana dashboard graphs

Use the sample Grafana dashboard as your starting point and customize it to suit your needs. You can click on the title of any graph, and then click **edit** to adjust the graphs as you see fit.

[Sample Grafana dashboard code](sample_grafana_dashboard_json_code.md)

|Graph name|Description|
|----------|-----------|
|Active requests|This graph serves as a "health check" for the Puppet Server. It shows a flat line that represents the number of CPUs you have in your system, a metric that indicates the total number of HTTP requests actively being processed by the server at any moment in time, and a rolling average of the number of active requests. If the number of requests being processed exceeds the number of CPUs for any significant length of time, your server might be receiving more requests than it can efficiently process.|
|Request durations|This graph breaks down the average response times for different types of requests made by Puppet agents. This indicates how expensive catalog and report requests are compared to the other types of requests. It also provides a way to see changes in catalog compilation times when you modify your Puppet code. A sharp curve upward for all of the types of requests indicates an overloaded server, and they should trend downward after reducing the load on the server.|
|Request ratios|This graph shows how many requests of each type that Puppet Server has handled. Under normal circumstances, you should see about the same number of catalog, node, or report requests, because these all happen one time per agent run. The number of file and file metadata requests correlate to how many remote file resources are in the agents' catalogs.|
|External HTTP Communications|This graph tracks the amount of time it takes Puppet Server to send data and requests for common operations to, and receive responses from, external HTTP services, such as PuppetDB.|
|File Sync|This graph tracks how long Puppet Server spends on File Sync operations, for both its storage and client services.|
|JRubies|This graph tracks how many JRubies are in use, how many are free, the mean number of free JRubies, and the mean number of requested JRubies. If the number of free JRubies is often less than one, or the mean number of free JRubies is less than one, Puppet Server is requesting and consuming more JRubies than are available. This overload reduces Puppet Server's performance. While this might simply be a symptom of an under-resourced server, it can also be caused by poorly optimized Puppet code or bottlenecks in the server's communications with PuppetDB if it is in use. If catalog compilation times have increased but PuppetDB performance remains the same, examine your Puppet code for potentially unoptimized code. If PuppetDB communication times have increased, tune PuppetDB for better performance or allocate more resources to it. If neither catalog compilation nor PuppetDB communication times are degraded, the Puppet Server process might be under-resourced on your server. If you have available CPU time and memory, increase the number of JRuby instances to allow it to allocate more JRubies. Otherwise, consider adding additional compilers to distribute the catalog compilation load.|
|JRuby Timers|This graph tracks several JRuby pool metrics. -   The borrow time represents the mean amount of time that Puppet Server uses \("borrows"\) each JRuby from the pool.
-   The wait time represents the total amount of time that Puppet Server waits for a free JRuby instance.
-   The lock held time represents the amount of time that Puppet Server holds a lock on the pool, during which JRubies cannot be borrowed. This occurs while Puppet Server synchronizes code for File Sync.
-   The lock wait time represents the amount of time that Puppet Server waits to acquire a lock on the pool.

 These metrics help identify sources of potential JRuby allocation bottlenecks.|
|Memory Usage|This graph tracks how much heap and non-heap memory that Puppet Server uses.|
|Compilation|This graph breaks catalog compilation down into various phases to show how expensive each phase is on the master.|

### Example Grafana dashboard excerpt

The following example shows only the `targets` parameter of a dashboard to demonstrate the full names of Puppet's exported Graphite metrics \(assuming the Puppet Server instance has a domain of `master.example.com`\) and a way to add targets directly to an exported Grafana dashboard's JSON content.

```
"panels": [
    {
        "span": 4,
        "editable": true,
        "type": "graphite",

...

        "targets": [
            {
                "target": "alias(puppetlabs.master.example.com.num-cpus,'num cpus')"
            },
            {
                "target": "alias(puppetlabs.master.example.com.http.active-requests.count,'active requests')"
            },
            {
                "target": "alias(puppetlabs.master.example.com.http.active-histo.mean,'average')"
            }
        ],
        "aliasColors": {},
        "aliasYAxis": {},
        "title": "Active Requests"
    }
]

```

See the sample Grafana dashboard for a detailed example of how a Grafana dashboard accesses these exported Graphite metrics.

