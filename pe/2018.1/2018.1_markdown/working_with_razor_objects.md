---
author: melissa amos <melissa.amos@puppet.com\>
---

# Working with Razor objects

Provisioning with Razor requires certain objects that define how nodes are provisioned.

-   **[Repositories](repositories.md)**  
A repository is where you store all of the actual bits used by Razor to install a node. Or, in some cases, the external location of bits that you link to. A repo is identified by a unique name.
-   **[Razor tasks](razor_tasks.md)**  
 Razor tasks describe a process or collection of actions that are performed when Razor provisions machines. Tasks can be used to designate an operating system or other software to install, where to get it, and the configuration details for the installation.
-   **[Tags](tags.md)**  
A tag consists of a unique `name` and a `rule`. Tags match a node if evaluating the node against the tag's facts results in `true`. Tag matching is case sensitive.
-   **[Policies](policies.md)**  
Policies tell Razor what bits to install, where to get the bits, how they should be configured, and how the installed node can communicate with Puppet Enterprise.
-   **[Brokers](brokers.md#)**  
Brokers hand off nodes to configuration management systems like Puppet Enterprise. Brokers consist of two parts: a broker type and information specific to the broker type.
-   **[Hooks](hooks.md#)**  
Hooks are an optional but useful Razor object. Hooks provide a way to run arbitrary scripts when certain events occur during a node's lifecycle. The behavior and structure of a hook are defined by a *hook type*.
-   **[Keeping Razor scalable](keeping_razor_scalable.md)**  
Speed provisioning and reduce load on the Razor server by following these scalability best practices.

