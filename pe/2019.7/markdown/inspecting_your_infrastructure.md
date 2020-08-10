---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Inspecting your infrastructure

The Puppet Enterprise console offers a variety of tools you can use to monitor the current state of your infrastructure, see the results of planned or unplanned changes to your Puppet code, and investigate problems. These tools are grouped in the Enforcement section of the console's sidebar.  

-   **[Monitoring current infrastructure state](monitor_infrastructure_state.md#)**  
When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is summarized on the Status page in the console.
-   **[Viewing and managing all packages in use](viewing_packages_in_use.md#)**  
The Packages page in the console shows all packages in use across your infrastructure by name, version, and provider, as well as the number of instances of each package version in your infrastructure. Use the Packages page to quickly identify which nodes are impacted by packages you know are eligible for maintenance updates, security patches, and license renewals.
-   **[Infrastructure reports](infrastructure_reports.md#)**  
Each time Puppet runs on a node, it generates a report that provides information such as when the run took place, any issues encountered during the run, and the activity of resources on the node. These reports are collected on the Reports page in the console.
-   **[Analyzing changes across Puppet runs](analyze_changes_across_runs.md#)**  
The Events page in the console shows a summary of activity in your infrastructure. You can analyze the details of important changes, and investigate common causes behind related events. You can also examine specific class, node, and resource events, and find out what caused them to fail, change, or run as no-op.
-   **[Viewing and managing Puppet Server metrics](puppet_server_metrics.md)**  
Puppet Server can provide performance and status metrics to external services for monitoring server health and performance over time.
-   **[Status API](status_api.md)**  
The status API allows you to check the health of PE components and services. It can be useful for automated monitoring of your infrastructure, removing unhealthy service instances from a load-balanced pool, checking configuration values, or troubleshooting issues in PE.

