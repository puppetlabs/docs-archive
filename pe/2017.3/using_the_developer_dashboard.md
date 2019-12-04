# Using the developer dashboard

While not as customizable as a Graphite dashboard, the developer dashboard is a simple, built-in way to view information about Puppet Server at a glance.

The developer dashboard features metrics particularly relevant to developers of Puppet manifests and modules, which are drawn from the Status API's metrics endpoints.

The dashboard charts the current and mean number of free and requested JRuby interpreters, as well as the mean JRuby borrow and wait times in milliseconds. It also lists the top 10 aggregate API endpoint requests, function calls, and resource declarations by total, mean, and aggregate counts. For more information about these metrics, see the documentation for the metrics endpoints.

## Accessing the developer dashboard

The developer dashboard, a tech preview, visualizes output from the Status API for reference when developing and deploying Puppet modules.

### About this task

The developer dashboard is enabled by default in Puppet Enterprise. To access the developer dashboard:

### Procedure

1.  Open a web browser and go to `https://<DNS NAME OF YOUR MASTER>:8140/puppet/experimental/dashboard.html`


