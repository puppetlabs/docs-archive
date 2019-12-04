---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Inspecting your infrastructure

The Puppet Enterprise console offers a variety of tools you can use to monitor the current state of your infrastructure, see the results of planned or unplanned changes to your Puppet code, and investigate problems. These tools are grouped in the Inspect section of the console's sidebar.  

-   **[Monitoring current infrastructure state](monitor_infrastructure_state.md#)**  
When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is summarized on the Overview page in the console.
-   **[Exploring your catalog with the node graph](explore_with_node_graph.md#)**  
The node graph provides a graphic representation of any node's configuration, shows you the relationships between classes and resources, and lets you gain greater insight into just how the Puppet master compiles your code and ships it to an agent. Visualizing relationships between resources helps you organize them, creating more reliable deployments.
-   **[Viewing and managing all packages in use](viewing_packages_in_use.md#)**  
The Packages page in the console shows all packages in use across your infrastructure by name, version, and provider, as well as the number of instances of each package version in your infrastructure. Use the Packages page to quickly identify which nodes are impacted by packages you know are eligible for maintenance updates, security patches, and license renewals.
-   **[Infrastructure reports](infrastructure_reports.md#)**  
Each time Puppet runs on a node, it generates a report that provides information such as when the run took place, any issues encountered during the run, and the activity of resources on the node. These reports are collected on the Reports page in the console.
-   **[Analyzing changes across Puppet runs](analyze_changes_across_runs.md#)**  
The Events page in the console shows a summary of activity in your infrastructure. You can analyze the details of important changes, and investigate common causes behind related events. You can also examine specific class, node, and resource events, and find out what caused them to fail, change, or run as no-op.
-   **[puppet\_server\_metrics.md](puppet_server_metrics.md)**  

-   **[Status API](status_api.md)**  
The status API allows you to check the health of PE components. It can be useful in automated monitoring of your PE infrastructure, removing unhealthy service instances from a load-balanced pool, checking configuration values, or when troubleshooting problems in PE.

