---
author: Logan Mantyla <logan.mantyla@puppet.com\>.
---

# Writing plans

Plans allow you to run more than one task with a single command, compute values for the input to a task, process the results of tasks, or make decisions based on the result of running a task.

Write plans in the Puppet language, giving them a `.pp` extension, and placing them in the module's `/plans` directory.

Plans can use any combination of Bolt functions or built-in Puppet functions.

-   **[Writing plans in YAML](writing_plans_in_yaml_pe.md#)**  
YAML plans run a list of steps in order, which allows you to define simple workflows. Steps can contain embedded Puppet code expressions to add logic where necessary.
-   **[Writing plans in Puppet Language](writing_plans_in_puppet_language_pe.md#)**  
Plans allow you to run more than one task with a single command, compute values for the input to a task, process the results of tasks, or make decisions based on the result of running a task.

**Parent topic:**[Orchestrating Puppet, tasks, and plans](orchestrating_puppet_and_tasks.md)

