# Analyzing changes across Puppet runs

The Events page in the console shows a summary of activity in your infrastructure. You can analyze the details of important changes, and investigate common causes behind related events. You can also examine specific class, node, and resource events, and find out what caused them to fail, change, or run as no-op.

## What is an event?

An event occurs whenever PE attempts to modify an individual property of a given resource. Reviewing events lets you see detailed information about what has changed on your system, or what isn't working properly.

During a Puppet run, Puppet compares the current state of each property on each resource to the desired state for that property, as defined by the node's catalog. If Puppet successfully compares the states and the property is already in sync \(in other words, if the current state is the desired state\), Puppet moves on to the next resource without noting anything. Otherwise, it attempts some action and records an event, which appears in the report it sends to the Puppet master at the end of the run. These reports provide the data presented on the Events page in the console.

## Event types

There are six types of event that can occur when Puppet reviews each property in your system and attempts to make any needed changes. If a property is already in sync with its catalog, no event is recorded: no news is good news in the world of events.

|Event|Description|
|-----|-----------|
|Failure|A property was out of sync; Puppet tried to make changes, but was unsuccessful.|
|Corrective change|Puppet found an inconsistency between the last applied catalog and a property's configuration, and corrected the property to match the catalog.|
|Intentional change|Puppet applied catalog changes to a property.|
|Corrective no-op|Puppet found an inconsistency between the last applied catalog and a property's configuration, but Puppet was instructed to not make changes on this resource, via either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter. Instead of making a corrective change, Puppet logs a corrective no-op event and reports the change it would have made.|
|Intentional no-op|Puppet would have applied catalog changes to a property., but Puppet was instructed to not make changes on this resource, via either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter. Instead of making an intentional change, Puppet logs an intentional no-op event and reports the change it would have made.|
|Skip|A prerequisite for this resource was not met, so Puppet did not compare its current state to the desired state. This prerequisite is either one of the resource's dependencies or a timing limitation set with the `schedule` metaparameter. The resource might be in sync or out of sync; Puppet doesn't know yet..

If the `schedule` metaparameter is set for a given resource, and the scheduled time hasn't arrived when the run happens, that resource logs a skip event on the Events page. This is true for a user-defined `schedule`, but does not apply to built-in scheduled tasks that happen weekly, daily, or at other intervals.

|

## Working with the Events page

During times when your deployment is in a state of stability, with no changes being made and everything functioning optimally, the Events page reports little activity, and might not seem terribly interesting. But when change occurs—when packages require upgrades, when security concerns threaten, or when systems fail—the Events page helps you understand what's happening and where so you can react quickly.

The Events page fetches data when loading, and does not refresh—even if there's a Puppet run while you're on the page—until you close or reload the page. This ensures that shifting data won't disrupt an investigation.

You can see how recent the shown data is by checking the timestamp at the top of the page. Reload the page to update the data to the most recent events.

**Tip:** Keeping time synchronized by running NTP across your deployment helps the Events page produce accurate information. NTP is easily managed with PE, and setting it up is an excellent way to learn Puppet workflows.

### Monitoring infrastructure with the Events summary pane

The Events page displays all events from the latest report of every responsive node in the deployment.

**Tip:** By default, PE considers a node unresponsive after one hour, but you can configure this setting to meet your needs by adjusting the `puppet_enterprise::console_services::no_longer_reporting_cutoff` parameter.

On the left side of the screen, the Events summary pane shows an overview of Puppet activity across your infrastructure. This data can help you rapidly assess the magnitude of any issue.

The Events summary pane is split into three categories—the Classes summary, Nodes summary, and Resources summary—to help you investigate how a change or failure event impacts your entire deployment.

### Gaining insight with the Events detail pane

Clicking an item in the Events summary pane loads its details \(and any sub-items\) in the Events detail pane on the right of the screen. The summary pane on the left always shows the list of items from which the one in the detail pane on the right was chosen, to let you easily view similar items and compare their states.

Click any item in the the Classes summary, Nodes summary, or Resources summary to load more specific info into the detail pane and begin looking for the causes of notable events. Switch between perspectives to find the common threads among a group of failures or corrective changes, and follow them to a root cause.

### Analyzing changes and failures

You can use the Events page to analyze the root causes of events resulting from a Puppet run. For example, to understand the cause of a failure after a Puppet run, select the class, node, or resource with a failure in the Events summary pane, and then review the details of the failure in the Events detail pane.

You can view additional details by clicking on the failed item in the in the Events detail pane.

Use the Classes summary, Nodes summary, and Resources summary to focus on the information you need. For example, if you’re concerned about a failed service, say Apache or MongoDB, you can start by looking into failed resources or classes. If you’re experiencing a geographic outage, you might start by drilling into failed node events.

### Understanding event display issues

In some special cases, events are not displayed as expected on the Events page. These cases are often caused by the way that the console receives data from other parts of Puppet Enterprise, but sometimes are due to the way your Puppet code is interpreted.

-   **Runs that restart PuppetDB are not displayed**

    If a given Puppet run restarts PuppetDB, Puppet is not able to submit a run report from that run to PuppetDB since PuppetDB is not available. Because the Events page relies on data from PuppetDB, and PuppetDB reports are not queued, the Events page does not display any events from that run. Note that in such cases, a run report *is* available on the Reports page. Having a Puppet run restart PuppetDB is an unlikely scenario, but one that could arise in cases where some change to, say, a parameter in the `puppetdb` class causes the `pe-puppetdb` service to restart.

-   **Runs without a compiled catalog are not displayed**

    If a run encounters a catastrophic failure where an error prevents a catalog from compiling, the Events page does not display any failures. This is because no events occurred.

-   **Simplified display for some resource types**

    For resource types that take the `ensure` property, such as user or file resource types, the Events page displays a single event when the resource is first created. This is because Puppet has changed only one property \(`ensure`\), which sets all the baseline properties of that resource at once. For example, all of the properties of a given user are created when the user is added, just as if the user was added manually. If a later Puppet run changes properties of that user resource, each individual property change is shown as a separate event.

-   **Updated modes display without leading zeros**

    When the `mode` attribute for a `file` resource is updated, and numeric notation is used, leading zeros are omitted in the **New Value** field on the **Events** page. For example, `0660` is shown as `660` and `0000` is shown as `0`.


