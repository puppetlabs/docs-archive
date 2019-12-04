---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Orchestrating Puppet and tasks

Puppet orchestrator is an effective tool for making on-demand changes to your infrastructure.

With orchestrator you can:

-   Automate tasks and eliminate manual work across your infrastructure and applications.
-   Initiate Puppet runs whenever you need to update agents.

-   Group the servers in your network according to immediate business needs. By leveraging orchestrator with [PuppetDB](https://puppet.com/docs/puppetdb/5.2/index.html), which stores detailed information about your nodes, you can search and filter servers based on metadata. No static lists of hosts, no reliance on complicated host-naming conventions.

-   Connect with thousands of hosts at the same time without slowing down your network. The Puppet Communications Protocol \(PCP\) and message broker efficiently mediate communications on your network even as your operational demands grow.

-   Distribute the Puppet agent workload by adding masters that are dedicated to catalog compilation as you increase in scale. Compile masters efficiently process requests and compile code for environments that have thousands of nodes. For more information, see [Installing compile masters](installing_compile_masters.md#).

-   Take advantage of the tasks installed with Puppet Enterprise. Use the package task to inspect, install, upgrade, and manage packages or the service task to start, stop, restart, and check the status of services running on your systems. Additional tasks are available from the Puppet [Forge](https://forge.puppet.com/).

-   Write your own tasks in any programming language that your target nodes can run, such as Bash, PowerShell, or Python.

-   Integrate server logging, auditing, and per node role-based access control \(RBAC\).


-   **[Running jobs with Puppet orchestrator](running_jobs_with_puppet_orchestrator_overview.md#)**  
With the Puppet orchestrator, you can run two types of "jobs": on-demand Puppet runs or Puppet tasks.
-   **[Configuring Puppet orchestrator](configuring_puppet_orchestrator.md#)**  
Once you've installed PE or the client tools package, there are a few tasks you need to do to prepare your PE infrastructure for orchestration services.
-   **[Using Bolt with orchestrator](bolt_configure_orchestrator.md#)**  
 Bolt enables running a series of tasks — called plans — to help you automate the manual work of maintaining your infrastructure. When you pair Bolt with PE, you get advanced automation with the management and logging capabilities of PE.
-   **[Direct Puppet: a workflow for controlling change](direct_puppet_a_workflow_for_controlling_change.md#)**  
The orchestrator—used alongside other PE tools, such as Code Manager—allows you to control when and how infrastructure changes are made before they reach your production environment.
-   **[Running Puppet on demand](running_puppet_on_demand_with_orch.md)**  
The orchestrator gives you the ability to set up jobs in the console or on the command line to trigger on-demand Puppet runs.
-   **[Running tasks](running_tasks.md)**  
Use the orchestrator to set up jobs in the console or on the command line and run tasks across systems in your infrastructure.
-   **[Writing tasks](writing_tasks.md#)**  
 Bolt tasks are similar to scripts, but they are kept in modules and can have metadata. This allows you to reuse and share them.
-   **[Reviewing jobs](reviewing_jobs.md)**  
You can review jobs—on-demand Puppet run, task, plan, or scheduled—from the console or the command line.
-   **[Puppet orchestrator API v1 endpoints](orchestrator_api_v1_endpoints.md)**  
Use this API to gather details about the orchestrator jobs you run.

