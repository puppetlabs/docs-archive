---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Managing MCollective

Puppet Enterprise includes the MCollective engine, which can invoke many kinds of actions in parallel across any number of nodes.

-   **[MCollective](mcollective_overview.md#)**  
With MCollective in Puppet Enterprise, you can invoke many kinds of actions in parallel across any number of nodes. Several useful actions are available by default, and you can easily add and use new actions.
-   **[Invoking actions](invoking_mcollective_actions.md#)**  
You can invoke MCollective actions on both \*nix and Windows machines, based on commands you run from the command line on a Linux-based Puppet master servers.
-   **[Controlling Puppet](controlling_puppet.md#)**  
PE's configuration management features rely on the Puppet agent service, which runs on every node and fetches configurations from the Puppet master server.
-   **[List of built-in actions](list_of_built_in_actions.md#)**  
MCollective in comes with several built-in actions. You invoke these actions from the command line.
-   **[Adding actions and plugins to PE](adding_actions_and_plugins.md#)**  
You can extend PE's MCollective engine by adding new actions. Actions are distributed in agent plugins, which are bundles of several related actions. You can write your own agent plugins \(or download ones created by other people\), and use PE to install and configure them on your nodes.
-   **[Disabling MCollective on some nodes](disabling_mcollective_on_some_nodes.md#)**  
By default, PE enables and configures MCollective on all agent nodes. This is generally desirable, but the code that manages this will not work on non-PE agent nodes, and will cause run failures on them.
-   **[Change the port used by MCollective/ActiveMQ](change_port_mcollective.md#)**  
You can change the port that MCollective/ActiveMQ uses with a simple variable change in the console.

