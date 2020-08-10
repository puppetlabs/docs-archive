# Available Graphite metrics

These HTTP and Puppet profiler metrics are available from the Puppet Server and can be added to your metrics reporting.

## Graphite metrics properties

Each metric is prefixed with `puppetlabs.<MASTER-HOSTNAME>`; for instance, the Grafana dashboard file refers to the `num-cpus` metric as `puppetlabs.<MASTER-HOSTNAME>.num-cpus`.

Additionally, metrics might be suffixed by fields, such as `count` or `mean`, that return more specific data points. For instance, the `puppetlabs.<MASTER-HOSTNAME>.compiler.mean` metric returns only the mean length of time it takes Puppet Server to compile a catalog.

To aid with reference, metrics in the list below are segmented into three groups:

-   **Statistical metrics:** Metrics that have all eight of these statistical analysis fields, in addition to the top-level metric:

    -   `max`: Its maximum measured value.

    -   `min`: Its minimum measured value.

    -   `mean`: Its mean, or average, value.

    -   `stddev`: Its standard deviation from the mean.

    -   `count`: An incremental counter.

    -   `p50`: The value of its 50th percentile, or median.

    -   `p75`: The value of its 75th percentile.

    -   `p95`: The value of its 95th percentile.

-   **Counters only:** Metrics that only count a value, or only have a `count` field.

-   **Other:** Metrics that have unique sets of available fields.


**Note:**

Puppet Server can export many metrics–so many that past versions of Puppet Enterprise could overwhelm Grafana servers. As of Puppet Enterprise 2016.4, Puppet Server exports only a subset of its available metrics by default. This set is designed to report the most relevant Puppet Server metrics for administrators monitoring its performance and stability.

To add to the default list of exported metrics, see Modifying Puppet Server's exported metrics.

Puppet Server exports each metric in the lists below by default.

## Statistical metrics

Compiler metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.compiler`: The time spent compiling catalogs. This metric represents the sum of the `compiler.compile`, `static_compile`, `find_facts`, and `find_node` fields.
    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.compile`: The total time spent compiling dynamic \(non-static\) catalogs.

        To measure specific nodes and environments, see Modifying Puppet Server's exported metrics.

    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.find_facts`: The time spent parsing facts.

    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.find_node`: The time spent retrieving node data. If the Node Classifier \(or another ENC\) is configured, this includes the time spent communicating with it.

    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.static_compile`: The time spent compiling static catalogs.

    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.static_compile_inlining`: The time spent inlining metadata for static catalogs.

    -   `puppetlabs.<MASTER-HOSTNAME>.compiler.static_compile_postprocessing`: The time spent post-processing static catalogs.


File sync metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-client.clone-timer`: The time spent by file sync clients on compilers initially cloning repositories on the master.

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-client.fetch-timer`: The time spent by file sync clients on compilers fetching repository updates from the master.

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-client.sync-clean-check-timer`: The time spent by file sync clients on compilers checking whether the repositories are clean.

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-client.sync-timer`: The time spent by file sync clients on compilers synchronizing code from the private datadir to the live codedir.

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-storage.commit-add-rm-timer`

-   `puppetlabs.<MASTER-HOSTNAME>.file-sync-storage.commit-timer`: The time spent committing code on the master into the file sync repository.


Function metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.functions`: The amount of time during catalog compilation spent in function calls. The `functions` metric can also report any of the statistical metrics fields for a single function by specifying the function name as a field.

    For example, to report the mean time spent in a function call during catalog compilation, use `puppetlabs.<MASTER-HOSTNAME>.functions.<FUNCTION-NAME>.mean`.


HTTP metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.http.active-histo`: A histogram of active HTTP requests over time.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-catalog-/*/-requests`: The time Puppet Server has spent handling catalog requests, including time spent waiting for an available JRuby instance.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environment-/*/-requests`: The time Puppet Server has spent handling environment requests, including time spent waiting for an available JRuby instance.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environment_classes-/*/-requests`: The time spent handling requests to the `environment_classes` API endpoint, which the Node Classifier uses to refresh classes.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environments-requests`: The time spent handling requests to the `environments` API endpoint requests made by the Orchestrator

-   The following metrics measure the time spent handling file-related API endpoints:

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_bucket_file-/*/-requests`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_content-/*/-requests`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_metadata-/*/-requests`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_metadatas-/*/-requests`

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-node-/*/-requests`: The time spent handling node requests, which are sent to the Node Classifier. A bottleneck here might indicate an issue with the Node Classifier or PuppetDB.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-report-/*/-requests`: The time spent handling report requests. A bottleneck here might indicate an issue with PuppetDB.

-   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-static_file_content-/*/-requests`: The time spent handling requests to the `static_file_content` API endpoint used by Direct Puppet with file sync.


JRuby metrics: Puppet Server uses an embedded JRuby interpreter to execute Ruby code. JRuby spawns parallel instances known as JRubies to execute Ruby code, which occurs during most Puppet Server activities. See Tuning JRuby on Puppet Server for details on adjusting JRuby settings.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.borrow-timer`: The time spent with a borrowed JRuby.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.free-jrubies-histo`: A histogram of free JRubies over time. This metric's average value must be greater than 1; if it isn't, more JRubies or another compiler might be needed to keep up with requests.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.lock-held-timer`: The time spent holding the JRuby lock.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.lock-wait-timer`: The time spent waiting to acquire the JRuby lock.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.requested-jrubies-histo`: A histogram of requested JRubies over time. This increases as the number of free JRubies, or the `free-jrubies-histo` metric, decreases, which can suggest that the server's capacity is being depleted.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.wait-timer`: The time spent waiting to borrow a JRuby.


PuppetDB metrics: The following metrics measure the time that Puppet Server spends sending or receiving data from PuppetDB.

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.catalog.save`

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.command.submit`

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.facts.find`

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.facts.search`

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.report.process`

-   `puppetlabs.<MASTER-HOSTNAME>.puppetdb.resource.search`


## Counters only

HTTP metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.http.active-requests`: The number of active HTTP requests.

-   The following counter metrics report the percentage of each HTTP API endpoint's share of total handled HTTP requests.

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-catalog-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environment-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environment_classes-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-environments-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_bucket_file-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_content-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_metadata-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-file_metadatas-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-node-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-report-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-resource_type-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-resource_types-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-static_file_content-/*/-percentage`

    -   `puppetlabs.<MASTER-HOSTNAME>.http.puppet-v3-status-/*/-percentage`

-   `puppetlabs.<MASTER-HOSTNAME>.http.total-requests`: The total requests handled by Puppet Server.


JRuby metrics:

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.borrow-count`: The number of successfully borrowed JRubies.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.borrow-retry-count`: The number of attempts to borrow a JRuby that must be retried.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.borrow-timeout-count`: The number of attempts to borrow a JRuby that resulted in a timeout.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.request-count`: The number of requested JRubies.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.return-count`: The number of JRubies successfully returned to the pool.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.num-free-jrubies`: The number of free JRuby instances. If this number is often 0, more requests are coming in than the server has available JRuby instances. To alleviate this, increase the number of JRuby instances on the Server or add additional compilers.

-   `puppetlabs.<MASTER-HOSTNAME>.jruby.num-jrubies`: The total number of JRuby instances on the server, governed by the `max-active-instances` setting. See Tuning JRuby on Puppet Server for details.


## Other metrics

These metrics measure raw resource availability and capacity.

-   `puppetlabs.<MASTER-HOSTNAME>.num-cpus`: The number of available CPUs on the server.

-   `puppetlabs.<MASTER-HOSTNAME>.uptime`: The Puppet Server process's uptime.

-   Total, heap, and non-heap memory that's committed \(`committed`\), initialized \(`init`\), and used \(`used`\), and the maximum amount of memory that can be used \(`max`\).

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.total.committed`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.total.init`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.total.used`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.total.max`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.heap.committed`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.heap.init`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.heap.used`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.heap.max`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.non-heap.committed`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.non-heap.init`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.non-heap.used`

    -   `puppetlabs.<MASTER-HOSTNAME>.memory.non-heap.max`


**Related information**  


[Modifying exported metrics](available_graphite_metrics.md#)

## Modifying exported metrics

In addition to the default metrics, you can also export metrics measuring specific environments and nodes managed by Puppet Server.

The `$metrics_puppetserver_metrics_allowed` class parameter in the `puppet_enterprise::profile::master` class takes an array of metrics as strings. To export additional metrics, add them to this array.

Optional metrics include:

-   `compiler.compile.<ENVIRONMENT>` and `compiler.compile.<ENVIRONMENT>.<NODE-NAME>`, and all statistical fields suffixed to these \(such as `compiler.compile.<ENVIRONMENT>.mean`\).

-   `compiler.compile.evaluate_resources.<RESOURCE>`: Time spent evaluating a specific resource during catalog compilation.


1.  Omit the `puppetlabs.<MASTER-HOSTNAME>` prefix and field suffixes \(such as `.count` or `.mean`\) from metrics. Instead, suffix the environment or node name as a field to the metric.

2.  For example, to track the compilation time for the `production` environment, add `compiler.compile.production` to the `metrics-allowed` list.

3.  To track only the `my.node.localdomain` node in the `production` environment, add `compiler.compile.production.my.node.localdomain` to the `metrics-allowed` list.


