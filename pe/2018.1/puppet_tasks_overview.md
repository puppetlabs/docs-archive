---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks in Puppet Enterprise

Puppet Enterprise allows you to run abitrary, or *ad hoc*, tasks \(as opposed to systematic Puppet configuration management changes\), and eliminate manual work across your infrastructure. A Puppet *task* is a single action that you execute on target machines by calling an executable file.

With Puppet tasks, you can troubleshoot and deploy changes to individual or multiple systems in your infrastructure.

Role-based access control \(RBAC\) determines who can run tasks on which parts your infrastructure. You can delegate access, even on a per-task basis, to ensure that teams or individuals can run tasks only on infrastructure they manage. Additionally, built-in reporting shows a complete history of task jobs.

You can run tasks from your tool of choice: the console, the orchestrator command line interface \(CLI\), or the orchestrator API `/command/task` endpoint.

Whatever tool you choose to run tasks with, you'll have the ability to filter nodes to target portions of an environment for task runs based on any attributes or events, such as your Windows nodes in your European data center, or all nodes with a specific version of OpenSSL installed. You can launch a task and check back later on the success of the task with the console or CLI.

Tasks are a type of orchestrator job. You can run a task job from either the console or the orchestrator CLI, and you can run it against a list of nodes, or against a PQL nodes query.

Running a task does not update your Puppet configuration. If you run a task that changes the state of a resource that Puppet is managing, a subsequent Puppet run will change the state of that resource back to what is defined in your Puppet configuration. For example, if you use a task to update the version of a managed package, the version of that package will be reset to whatever is specified in a manifest on the next Puppet run.

