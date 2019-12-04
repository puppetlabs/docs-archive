# New features

These are the features added to PE 2017.3.x.

## AWS OpsWorks for Puppet Enterprise

Puppet Enterprise 2017.3 is now available as a managed service from AWS OpsWorks.

AWS OpsWorks for Puppet Enterprise offers cloud-focused workflows and managed service capabilities for running Puppet Enterprise on AWS, including automatic backups, restorations, and software upgrades.  

User documentation for AWS OpsWorks for Puppet Enterprise is available at the [AWS OpsWorks documentation site](http://docs.aws.amazon.com/opsworks/latest/userguide/welcome.html). 

## Ad-hoc tasks

PE 2017.3 adds tools for running *ad hoc* tasks on nodes in your infrastructure.

You can run tasks from the console, on the command line, or by the orchestrator API `/command/task` endpoint. You can target an individual node, a list of nodes, or a set of nodes derived from a PQL query. Watch task execution in real time from the console or command line, and take advantage of PE's built-in job reporting and activity service that shows a complete history of task jobs. Role-based access control allows you to define who can run which tasks.

Tasks are packaged in modules, and this release includes some default tasks—`facter_task`, `package`, `service`, and `puppet_conf`—to get you started. You can install many more from the Forge, or write your own.

## Hiera overrides in the console

PE 2017.3 adds configuration data in the console, where you can now set parameters on node groups without declaring the class. Data that is set in the console is used for automatic parameter lookup, which promotes code regularity and predictability.

Console configuration data overrides default or missing parameter values on classes that are already declared. This capability resolves the class conflicts that previously prevented using both the node classifier and the roles and profiles method.

You can set configuration data on the **Configuration** tab \(formerly the **Classes** tab\). A new node group permission, **Edit configuration data**, controls the ability to create and edit configuration data. Data editing is enabled by default on new installations, but you must enable it on upgrades from previous versions.

**Related topics**  


[Enable data editing in the console](grouping_and_classifying_nodes.md#)

[Set configuration data](grouping_and_classifying_nodes.md#)

## Package inventory and management

PE 2017.3 includes improvements to the Packages page, where you can now view and manage your complete package inventory.

The new packages details page allows you to:

-   Separate package instances that are managed with Puppet from those that are not managed with Puppet.

-   Filter packages by version, and generate lists of affected nodes.

-   See where packages managed with Puppet are declared in your Puppet code, and quickly navigate to that location.

-   Run tasks to update packages that are not managed with Puppet.


## New and improved docs

PE 2017.3 coincides with some structural changes to documentation.

-   **A new home**

    Docs now live at [puppet.com/docs](https://puppet.com/docs) instead of at [docs.puppet.com](https//docs.puppet.com/). This change improves search capabilities for online Puppet content.

-   **Refreshed and re-organized content**

    We've restructured the PE and much of the Puppet docs to be more clear, user-friendly, and navigable. We've moved content and renamed it in the process, so you'll have to update some bookmarks. Sorry for the inconvenience, but it had to be done.

-   **Getting started**

    We've renamed the former quick start guides to *Getting started guides*, and added steps for using the Puppet Development Kit to get you started with writing, verifying, and testing your modules with the latest tools.


