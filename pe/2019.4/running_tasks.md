---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks in PE

Use the orchestrator to set up jobs in the console or on the command line and run Bolt tasks across systems in your infrastructure.

**Note:** If you have set up compilers and you want to use tasks, you must either set `master_uris` or you `server_list` on agents to point to your compilers. This setting is described in the section on configuring compilers for orchestrator scale.

-   **[Running tasks from the console](running_tasks_in_the_console.md#)**  
Run ad-hoc tasks on target machines to upgrade packages, restart services, or perform any other type of single-action executions on your nodes.
-   **[Running tasks from the command line](running_tasks_from_the_command_line.md#)**  
Use the `puppet task run` command to run tasks on agent nodes.

**Parent topic:**[Tasks and plans in PE](running_tasks_and_plans_pe.md)

**Related information**  


[Configure compilers](installing_compile_masters.md#)

