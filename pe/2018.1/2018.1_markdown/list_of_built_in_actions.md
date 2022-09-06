# List of built-in actions

MCollective in comes with several built-in actions. You invoke these actions from the command line.

## MCollective actions and plugins

Sets of related actions are bundled together as MCollective agent plugins. Every action is part of a plugin.

A default PE install includes the package, puppet, puppetral, rpcutil, and service plugins. See the table of contents above for an outline of each plugin's actions; click an action for details about its inputs, effects, and outputs.

You can easily add new actions by distributing custom agent plugins to your nodes. See [Adding actions](adding_actions_and_plugins.md#) for details.

### The `package` plugin

Install and uninstall software packages.

Actions: `apt_checkupdates`, `apt_update`, `checkupdates`, `install`, `purge`, `status`, `uninstall`, `update`, `yum_checkupdates`, `yum_clean`

-   `apt_checkupdates`: Check for APT updates

    |Outputs|Definition|
    |-------|----------|
    |`exitcode`|\(Appears as "Exit Code" on CLI\). The exitcode from the apt command|
    |`outdated_packages`|\(Appears as "Outdated Packages" on CLI\) Outdated packages|
    |`output`|\(Appears as "Output" on CLI\) Output from APT|

-   `apt_update`: Update the apt cache

    |Outputs|Definition|
    |-------|----------|
    |`exitcode`|\(Appears as "Exit Code" on CLI\) The exitcode from the apt-get command|
    |`output`|\(Appears as "Output" on CLI\) Output from apt-get|

-   `checkupdates`: Check for updates

    |Outputs|Definition|
    |-------|----------|
    |`exitcode`|\(Appears as "Exit Code" on CLI\) The exitcode from the package manager command|
    |`outdated_packages`|\(Appears as "Outdated Packages" on CLI\) Outdated packages|
    |`output`|\(Appears as "Output" on CLI\) Output from Package Manager|
    |`package_manager`|\(Appears as "Package Manager" on CLI\) The detected package manager|

-   `install`: Install a package

    |Input|Definition|
    |-----|----------|
    |`package` **\(required\)**|Package to install **Type:** string, **Format/Validation:**`shellsafe`, **Length:** 90|

    |Outputs|Definition|
    |-------|----------|
    |`arch`|\(Appears as "Arch" on CLI\) Package architecture|
    |`ensure`|\(Appears as "Ensure" on CLI\) Full package version|
    |`epoch`|\(Appears as "Epoch" on CLI\) Package epoch number|
    |`name`|\(Appears as "Name" on CLI\) Package name|
    |`output`|\(Appears as "Output" on CLI\) Output from the package manager|
    |`provider`|\(Appears as "Provider" on CLI\) Provider used to retrieve information|
    |`release`|\(Appears as "Release" on CLI\) Package release number|
    |`version`|\(Appears as "Version" on CLI\) Version number|

-   `purge`: Purge a package

    |Input|Definition|
    |-----|----------|
    |`package` **\(required\)**|Package to purge **Type:** string, **Format/Validation:**`shellsafe`, **Length:** 90|

    |Outputs|Definition|
    |-------|----------|
    |`arch`|\(Appears as "Arch" on CLI\) Package architecture|
    |`ensure`|\(Appears as "Ensure" on CLI\) Full package version|
    |`epoch`|\(Appears as "Epoch" on CLI\) Package epoch number|
    |`name`|\(Appears as "Name" on CLI\) Package name|
    |`output`|\(Appears as "Output" on CLI\) Output from the package manager|
    |`provider`|\(Appears as "Provider" on CLI\) Provider used to retrieve information|
    |`release`|\(Appears as "Release" on CLI\) Package release number|
    |`version`|\(Appears as "Version" on CLI\) Version number|

-   `status`: Get the status of a package

    |Input|Definition|
    |-----|----------|
    |`package` **\(required\)**|Package to retrieve the status of **Type:** string **Format/Validation:**`shellsafe`**Length:** 90|

    |Outputs|Defintion|
    |-------|---------|
    |`arch`|\(Appears as "Arch" on CLI\) Package architecture|
    |`ensure`|\(Appears as "Ensure" on CLI\) Full package version|
    |`epoch`|\(Appears as "Epoch" on CLI\) Package epoch number|
    |`name`|\(Appears as "Name" on CLI\) Package name|
    |`output`|\(Appears as "Output" on CLI\) Output from the package manager|
    |`provider`|\(Appears as "Provider" on CLI\) Provider used to retrieve information|
    |`release`|\(Appears as "Release" on CLI\)Package release number|
    |`version`|\(Appears as "Version" on CLI\) Version number|

-   `uninstall`: Uninstall a package

    |Input|Defintion|
    |-----|---------|
    |`package` **\(required\)**|Package to uninstall **Type:** string **Format/Validation:**`shellsafe`**Length:** 90|

    |Outputs|Defintion|
    |-------|---------|
    |`arch`|\(Appears as "Arch" on CLI\) Package architecture|
    |`ensure`|\(Appears as "Ensure" on CLI\) Full package version|
    |`epoch`|\(Appears as "Epoch" on CLI\) Package epoch number|
    |`name`|\(Appears as "Name" on CLI\) Package name|
    |`output`|\(Appears as "Output" on CLI\) Output from the package manager|
    |`provider`|\(Appears as "Provider" on CLI\) Provider used to retrieve information|
    |`release`|\(Appears as "Release" on CLI\)Package release number|
    |`version`|\(Appears as "Version" on CLI\) Version number|

-   `update`: Update a package

    |Input|Defintion|
    |-----|---------|
    |`package` **\(required\)**|Package to updatge **Type:** string **Format/Validation:**`shellsafe`**Length:** 90|

    |Outputs|Defintion|
    |-------|---------|
    |`arch`|\(Appears as "Arch" on CLI\) Package architecture|
    |`ensure`|\(Appears as "Ensure" on CLI\) Full package version|
    |`epoch`|\(Appears as "Epoch" on CLI\) Package epoch number|
    |`name`|\(Appears as "Name" on CLI\) Package name|
    |`output`|\(Appears as "Output" on CLI\) Output from the package manager|
    |`provider`|\(Appears as "Provider" on CLI\) Provider used to retrieve information|
    |`release`|\(Appears as "Release" on CLI\)Package release number|
    |`version`|\(Appears as "Version" on CLI\) Version number|

-   `yum_checkupdates`: Check for YUM updates

    |Outputs|Definition|
    |-------|----------|
    |`exitcode`|\(Appears as "Exit Code" on CLI\) The exitcode from the yum command|
    |`outdated_packages`|\(Appears as "Outdated Packages" on CLI\) Outdated packages|
    |`output`|\(Appears as "Output" on CLI\) Output from Yum|

-   `yum_clean`: Clean the YUM cache

    |Input|Definition|
    |-----|----------|
    |`mode`|One of the various supported clean modes **Type:** list **Valid Values:**`all`, `headers`, `packages`, `metadata`, `dbcache`, `plugins`, `expire-cache`|

    |Outputs|Definition|
    |-------|----------|
    |`exitcode`|\(Appears as "Exit Code" on CLI\) The exitcode from the yum command|
    |`output`|\(Appears as "Output" on CLI\) Output from Yum|


### The `puppet` plugin

Run Puppet agent, get its status, and enable/disable it

Actions: `disable`, `enable`, `last_run_summary`, `resource`, `runonce`, `status`

-   `disable`: Disable the Puppet agent

    |Input|Definition|
    |-----|----------|
    |`message`|Supply a reason for disabling the Puppet agent **Type:** string **Format/Validation:**`shellsafe`**Length:** 120|

    |Outputs|Definition|
    |-------|----------|
    |`enabled`|\(Appears as "Enabled" on CLI\) Is the agent currently locked|
    |`status`|\(Appears as "Status" on CLI\) Status|

-   `enable`: Enable the Puppet agent

    |Outputs|Definition|
    |-------|----------|
    |`enabled`|\(Appears as "Enabled" on CLI\) Is the agent currently locked|
    |`status`|\(Appears as "Status" on CLI\) Status|

-   `last_run_summary`: Get the summary of the last Puppet run

    |Outputs|Definition|
    |-------|----------|
    |`changed_resources`|\(Appears as "Changed Resources" on CLI\) Resources that were changed|
    |`config_retrieval_time`|\(Appears as "Config Retrieval Time" on CLI\) Time taken to retrieve the catalog from the master|
    |`config_version`|\(Appears as "Config Version" on CLI\) Puppet config version for the previously applied catalog|
    |`failed_resources`|\(Appears as "Failed Resources" on CLI\) Resources that failed to apply|
    |`lastrun`|\(Appears as "Last Run" on CLI\) When the Agent last applied a catalog in local time|
    |`out_of_sync_resources`|\(Appears as "Out of Sync Resources" on CLI\) Resources that were not in desired state|
    |`since_lastrun`|\(Appears as "Since Last Run" on CLI\) How long ago did the Agent last apply a catalog in local time|
    |`summary`|\(Appears as "Summary" on CLI\) Summary data as provided by Puppet|
    |`total_resources`|\(Appears as "Total Resources" on CLI\) Total resources managed on a node|
    |`total_time`|\(Appears as "Total Time" on CLI\) Total time taken to retrieve and process the catalog|
    |`type_distribution`|\(Appears as "Type Distribution" on CLI\) Resource counts per type managed by Puppet|

-   `resource`: Evaluate Puppet RAL resources

    |Inputs|Definition|
    |------|----------|
    |`name` **\(required\)**|Resource Name **Type:** string **Format/Validation:**`^.+$`**Length:** 150|
    |`type` **\(required\)**|Resource Type **Type:** string **Format/Validation:**`^.+$`**Length:** 50|

    |Outputs|Definition|
    |-------|----------|
    |`changed`|\(Appears as "Changed" on CLI\) Was a change applied based on the resource|
    |`result`|\(Appears as "Result" on CLI\) The result from the Puppet resource|

-   `runonce`: Invoke a single Puppet run

    |Inputs|Definition|
    |------|----------|
    |`environment`|Which Puppet environment to run **Type:** string **Format/Validation:**`puppet_variable`**Length:** 50|
    |`force`|Will force a run immediately else is subject to default splay time **Type:** boolean|
    |`noop`|Do a Puppet dry run **Type:** boolean|
    |`server`|Address and port of the Puppet Master in server:port format **Type:** string **Format/Validation:**`puppet_server_address`**Length:** 50|
    |`splay`|Sleep for a period before initiating the run **Type:** boolean|
    |`splaylimit`|Maximum amount of time to sleep before run **Type:** number|
    |`tags`|Restrict the Puppet run to a comma list of tags **Type:** strings **Format/Validation:**`puppet_tags`**Length:** 120|

    |Output|Definition|
    |------|----------|
    |`summary`|\(Appears as "Summary" on CLI\) Summary of command run|

-   `status`: Get the current status of the Puppet agent

    |Outputs|Definition|
    |-------|----------|
    |`applying`|\(Appears as "Applying" on CLI\) Is a catalog being applied|
    |`daemon_present`|\(Appears as "Daemon Running" on CLI\) Is the Puppet agent daemon running on this system|
    |`disable_message`|\(Appears as "Lock Message" on CLI\) Message supplied when agent was disabled|
    |`enabled`|\(Appears as "Enabled" on CLI\) Is the agent currently locked|
    |`idling`|\(Appears as "Idling" on CLI\) Is the Puppet agent daemon running but not doing any work|
    |`lastrun`|\(Appears as "Last Run" on CLI\) When the Agent last applied a catalog in local time|
    |`since_lastrun`|\(Appears as "Since Last Run" on CLI\) How long ago did the Agent last apply a catalog in local time|
    |`status`|\(Appears as "Status" on CLI\) Current status of the Puppet agent|


### The `puppetral` plugin

View resources with Puppet's resource abstraction layer

Actions: `find`, `search`

-   `find`: Get the attributes and status of a resource

    |Inputs|Definition|
    |------|----------|
    |`title` **\(required\)**|Name of resource to check **Type:** string **Format/Validation:**`.`**Length:** 90|
    |`type` **\(required\)**|Type of resource to check **Type:** string **Format/Validation:**`.`**Length:** 90|

    |Outputs|Definition|
    |-------|----------|
    |`exported`|\(Appears as "Exported" on CLI\) Boolean flag indicating export status|
    |`managed`|\(Appears as "Managed" on CLI\) Flag indicating managed status|
    |`parameters`|\(Appears as "Parameters" on CLI\) Parameters of the inspected resource|
    |`tags`|\(Appears as "Tags" on CLI\) Tags of the inspected resource|
    |`title`|\(Appears as "Title" on CLI\) Title of the inspected resource|
    |`type`|\(Appears as "Type" on CLI\) Type of the inspected resource|

-   `search`: Get detailed info for all resources of a given type

    |Inputs|Definition|
    |------|----------|
    |`type` **\(required\)**|Type of resource to check **Type:** string **Format/Validation:**`.`**Length:** 90|

    |Output|Definition|
    |------|----------|
    |`result`|\(Appears as "Result" on CLI\) The values of the inspected resources|


### The `rpcutil` plugin

General helpful actions that expose stats and internals to SimpleRPC clients

Actions: `agent_inventory`, `collective_info`, `daemon_stats`, `get_config_item`, `get_data`, `get_fact`, `inventory`, `ping`

-   `agent_inventory`: Inventory of all agents on the server

    |Output|Definition|
    |------|----------|
    |`agents`|\(Appears as "Agents" on CLI\) List of agents on the server|

-   `collective_info`: Info about the main and sub collectives

    |Outputs|Definition|
    |-------|----------|
    |`collectives`|\(Appears as "All Collectives" on CLI\) All Collectives|
    |`main_collective`|\(Appears as "Main Collective" on CLI\) The main Collective|

-   `daemon_stats`: Get statistics from the running daemon \(no inputs\)

    |Outputs|Definition|
    |-------|----------|
    |`agents`|\(Appears as "Agents" on CLI\) List of agents loaded|
    |`configfile`|\(Appears as "Config File" on CLI\) Config file used to start the daemon|
    |`filtered`|\(Appears as "Failed Filter" on CLI\) Didn't pass filter checks|
    |`passed`|\(Appears as "Passed Filter" on CLI\) Passed filter checks|
    |`pid`|\(Appears as "PID" on CLI\) Process ID of the daemon|
    |`replies`|\(Appears as "Replies" on CLI\) Replies sent back to clients|
    |`starttime`|\(Appears as "Start Time" on CLI\) Time the server started|
    |`threads`|\(Appears as "Threads" on CLI\) List of threads active in the daemon|
    |`times`|\(Appears as "Times" on CLI\) Processor time consumed by the daemon|
    |`total`|\(Appears as "Total Messages" on CLI\) Total messages received|
    |`ttlexpired`|\(Appears as "TTL Expired" on CLI\) Messages that did pass TTL checks|
    |`unvalidated`|\(Appears as "Failed Security" on CLI\) Messages that failed security validation|
    |`validated`|\(Appears as "Security Validated" on CLI\) Messages that passed security validation|
    |`version`|\(Appears as "Version" on CLI\) MCollective Version|

-   `get_config_item`: Get the active value of a specific config property

    |Input|Definition|
    |-----|----------|
    |`item` **\(required\)**|The item to retrieve from the server **Type:** string **Format/Validation:**`^.+$`**Length:** 50|

    |Outputs|Definition|
    |-------|----------|
    |`item`|\(Appears as "Property" on CLI\) The config property being retrieved|
    |`value`|\(Appears as "Value" on CLI\) The value that is in use|

-   `get_data`: Get data from a data plugin

    |Inputs|Definition|
    |------|----------|
    |`query`|The query argument to supply to the data plugin **Type:** string **Format/Validation:**`^.+$`**Length:** 50|
    |`source` **\(required\)**|The data plugin to retrieve information from **Type:** string **Format/Validation:**`^\w+$`**Length:** 50|

-   `get_fact`: Retrieve a single fact from the fact store

    |Input|Definition|
    |-----|----------|
    |`fact` **\(required\)**|The fact to retrieve **Type:** string **Format/Validation:**`^[\w\-\.]+$`**Length:** 40|

    |Outputs|Definition|
    |-------|----------|
    |`fact`|\(Appears as "Fact" on CLI\) The name of the fact being returned|
    |`value`|\(Appears as "Value" on CLI\) The value of the fact|

-   `inventory`: System Inventory

    |Outputs|Definition|
    |-------|----------|
    |`agents`|\(Appears as "Agents" on CLI\) List of agent names|
    |`classes`|\(Appears as "Classes" on CLI\) List of classes on the system|
    |`collectives`|\(Appears as "All Collectives" on CLI\) All Collectives|
    |`data_plugins`|\(Appears as "Data Plugins" on CLI\) List of data plugin names|
    |`facts`|\(Appears as "Facts" on CLI\) List of facts and values|
    |`main_collective`|\(Appears as "Main Collective" on CLI\) The main Collective|
    |`version`|\(Appears as "Version" on CLI\) MCollective Version|

-   `ping`: Responds to requests for PING with PONG

    |Output|Definition|
    |------|----------|
    |`pong`|\(Appears as "Timestamp" on CLI\) The local timestamp|


### The `service` plugin

Start and stop system services

Actions: `restart`, `start`, `status`, `stop`

-   `restart`: Restart a service

    |Input|Definition|
    |-----|----------|
    |`service` **\(required\)**|The service to restart **Type:** string **Format/Validation:**`service_name`**Length:** 90|

    |Output|Definition|
    |------|----------|
    |`status`|\(Appears as "Service Status" on CLI\) The status of the service after restarting|

-   `start`: Start a service

    |Input|Definition|
    |-----|----------|
    |`service` **\(required\)**|The service to start **Type:** string **Format/Validation:**`service_name`**Length:** 90|

    |Output|Definition|
    |------|----------|
    |`status`|\(Appears as "Service Status" on CLI\) The status of the service after starting|

-   `status`: Gets the status of a service

    |Input|Definition|
    |-----|----------|
    |`service` **\(required\)**|The service to get the status for **Type:** string **Format/Validation:**`service_name`**Length:** 90|

    |Output|Definition|
    |------|----------|
    |`status`|\(Appears as "Service Status" on CLI\) The status of the service|

-   `stop`: Stop a service

    |Input|Definition|
    |-----|----------|
    |`service` **\(required\)**|The service to stop **Type:** string **Format/Validation:**`service_name`**Length:** 90|

    |Output|Definition|
    |------|----------|
    |`status`|\(Appears as "Service Status" on CLI\) The status of the service after stopping|


