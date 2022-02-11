---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Managing MCollective

The MCollective engine can invoke many kinds of actions in parallel across any number of nodes.

CAUTION:

Puppet Enterprise 2018.1 is the last release to support Marionette Collective, also known as MCollective. While PE 2018.1 remains supported, Puppet will continue to address security issues for MCollective. Feature development has been discontinued. Future releases of PE will not include MCollective. For more information, see the [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).

To prepare for these changes, migrate your MCollective work to Puppet orchestrator to automate tasks and create consistent, repeatable administrative processes. Use orchestrator to automate your workflows and take advantage of its integration with Puppet Enterprise console and commands, APIs, role-based access control, and event tracking.

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
-   **[Disabling MCollective](disabling_mcollective_nodes.md#)**  
Turn off MCollective on a single node or on all nodes.
-   **[Change the port used by MCollective/ActiveMQ](change_port_mcollective.md#)**  
You can change the port that MCollective/ActiveMQ uses with a simple variable change in the console.
-   **[Moving from MCollective to Puppet orchestrator](migrating_from_mcollective_to_orchestrator.md#)**  
 Puppet Enterprise 2018.1 is the last release to support MCollective. To prepare for these changes, migrate your MCollective work to  Puppet orchestrator to automate tasks and create consistent, repeatable administrative processes.
-   **[Removing MCollective](removing_mcollective.md)**  
Remove MCollective and its related files from the nodes in your infrastructure.

