---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks

The orchestrator gives you the ability to set up jobs in the console or on the command line to run ad hoc or arbitrary tasks across systems in your infrastructure.

You can install pre-existing tasks, and run tasks from the console and from the command line.

**Note:** If you have set up compile masters and you want to use tasks, you must either set `master_uris` or you `server_list`on agents to point to to your compile masters. This setting is described in the section on configuring compile masters for orchestrator scale.

## Writing tasks and plans

You can also write your own tasks in any programming language that can run on your target nodes, such as Bash, Python, or Ruby. Tasks are packaged within modules, so you can reuse, download, and share tasks on the Forge. Task metadata describes the task, validates input, and controls how the task runner executes the task.

**Note:** The ability to write task plans, which tie tasks together for more complex operations, is currently experimental.

For information about [writing tasks and task plans](https://puppet.com/docs/bolt/0.5/writing_tasks_and_plans.html), see the Bolt documentation. For information about installing and running tasks, read on in this section.

-   **[Running tasks in Puppet Enterprise](puppet_tasks_overview.md)**  
Puppet Enterprise allows you to run abitrary, or *ad hoc*, tasks \(as opposed to systematic Puppet configuration management changes\), and eliminate manual work across your infrastructure. A Puppet *task* is a single action that you execute on target machines by calling an executable file.
-   **[Installing tasks](installing_tasks.md)**  
Puppet Enterprise comes with some pre-installed tasks, but you must install any other tasks you want to use.
-   **[Running tasks in the console](running_tasks_in_the_console.md#)**  
PE gives you the ability to execute ad-hoc tasks on target machines from the console. Task are the quickest way to upgrade packages, restart services, or perform any other type of single-action executions on your nodes.
-   **[Running tasks from the command line](running_tasks_from_the_command_line.md#)**  
Use the `puppet task run` command to run tasks on agent nodes. For example, do you want to inspect a package or quickly stop a particular service? Use the `puppet task` tool and the relevant module to make those changes arbitrarily, rather than through a Puppet configuration change.

**Related topics**  


[Configure compile masters for orchestrator scale](installing_compile_masters.md#)

