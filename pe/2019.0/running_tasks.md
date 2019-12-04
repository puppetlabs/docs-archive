---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks

Use the orchestrator to set up jobs in the console or on the command line and run tasks across systems in your infrastructure.

You can install pre-existing tasks, and run tasks from the console and from the command line.

**Note:** If you have set up compile masters and you want to use tasks, you must either set `master_uris` or you `server_list` on agents to point to to your compile masters. This setting is described in the section on configuring compile masters for orchestrator scale.

-   **[Running tasks in Puppet Enterprise](puppet_tasks_overview.md)**  
 Run abitrary, or *ad hoc*, tasks \(as opposed to systematic Puppet configuration management changes\), and eliminate manual work across your infrastructure.
-   **[Installing tasks](installing_tasks.md)**  
 Puppet Enterprise comes with some pre-installed tasks, but you must install any other tasks you want to use.
-   **[Running tasks from the console](running_tasks_in_the_console.md#)**  
Run ad-hoc tasks on target machines to upgrade packages, restart services, or perform any other type of single-action executions on your nodes.
-   **[Running tasks from the command line](running_tasks_from_the_command_line.md#)**  
Use the `puppet task run` command to run tasks on agent nodes.

**Related information**  


[Configure compile masters](installing_compile_masters.md#)

