---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Inspecting plans

View the plans you have installed and which ones you have permissions to run, as well as details about individual plans.

To see information about plans, log into your master or client tools workstation and run one of the following commands:

-   `puppet plan show` - View a list of your permitted plans.

-   `puppet plan show --all` - View a list of all installed plans.

There is currently no way to view plan metadata using the `puppet plan show` function. See [Orchestration services known issues](known_issues_pe.md#) for more information.

