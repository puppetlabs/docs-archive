---
author: Garrett Guillotte <garrett.guillotte@puppet.com\>
---

# Viewing and managing Puppet Server metrics

Puppet Server can provide performance and status metrics to external services for monitoring server health and performance over time.

You can track Puppet Server metrics by using:

-   Customizable, networked Graphite and Grafana instances
-   A built-in experimental developer dashboard
-   Status API endpoints

**Note:** None of these methods are officially supported. The Grafanadash and Puppet-graphite modules referenced in this document are not Puppet-supported modules; they are mentioned for testing and demonstration purposes only. The developer dashboard is a tech preview. Both the Grafana and developer dashboard methods take advantage of the Status API, including some endpoints that are also a tech preview.

-   **[Getting started with Graphite](getting_started_with_graphite.md#)**  
Puppet Enterprise can export many metrics to Graphite, a third-party monitoring application that stores real-time metrics and provides customizable ways to view them. After Graphite support is enabled, Puppet Server exports a set of metrics by default that is designed to be immediately useful to Puppet administrators.
-   **[Available Graphite metrics](available_graphite_metrics.md#)**  
These HTTP and Puppet profiler metrics are available from the Puppet Server and can be added to your metrics reporting.

