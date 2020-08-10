---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Tasks and plans in PE

Use the orchestrator to run tasks and plans in the console or on the command line.

You can install pre-existing tasks and plans and run them from the console and from the command line.

**Note:** If you have set up compilers and you want to use tasks and/or plans, you must either set `master_uris` or you `server_list` on agents to point to your compilers. This setting is described in the section on configuring compilers for orchestrator scale.

A *task* is a single action that you execute on target machines. With tasks, you can troubleshoot and deploy changes to individual or multiple systems in your infrastructure.

A *plan* is a bundle of tasks that can be combined with other logic. They allow you to do complex task operations, like running multiple tasks with one command or running certain tasks based on the output of another task.

You can run tasks and plans from your tool of choice: the console, the orchestrator command line interface \(CLI\), or the orchestrator API `/command/task` and `/command/plan_jobs` endpoint.

You can launch a task or plan and check on the status or view the output later with the console or CLI.

Running a task or plan does not update your Puppet configuration. If you run a task or plan that changes the state of a resource that Puppet is managing, a subsequent Puppet run changes the state of that resource back to what is defined in your Puppet configuration. For example, if you use a task to update the version of a managed package, the version of that package is reset to whatever is specified in a manifest on the next Puppet run.

**Note:** If you are running multiple tasks and plans, make sure your concurrency limit for tasks and bolt-server can accommodate your needs. See [Configure the orchestrator and pe-orchestration-services](config_orchestration.md#) to adjust these settings.

## RBAC for tasks and plans

Tasks rely on role-based access control \(RBAC\) to determine who can run which tasks on your infrastructure. You can delegate access, even on a per-task basis, to ensure that teams or individuals can run task and plan jobs only on infrastructure they manage.

RBAC for plans and tasks do **not** intersect. This means that if a user does not have access to a specific task but they have access to run a plan containing that task, they are still able to run the plan. This allows you to implement more customized security options by wrapping tasks within plans. Read more about RBAC considerations for writing plans [here](writing_plans_in_puppet_language_pe.md#).

-   **[Installing tasks and plans](installing_tasks.md#)**  
 Puppet Enterprise comes with some pre-installed tasks, but you must install all plans or any other tasks you want to use.
-   **[Running tasks in PE](running_tasks.md)**  
Use the orchestrator to set up jobs in the console or on the command line and run Bolt tasks across systems in your infrastructure.
-   **[Running plans in PE](running_plans_in_pe.md)**  
Use the orchestrator to set up jobs in the console or on the command line and run plans across systems in your infrastructure.

