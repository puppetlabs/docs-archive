---
layout: default
title: "Analyze changes across Puppet runs"
canonical: "/pe/latest/CM_state_inspector.html"
---

The Events page in the PE console lets you view a summary of activity in your infrastructure, analyze the details of important changes, and investigate common causes behind related events. 

## What is an event? 

An **event** occurs whenever PE attempts to modify an individual property of a given resource. Reviewing events lets you see detailed information about what has changed on your system, or what isn't working properly.    

During a Puppet run, Puppet compares the current state of each property on each resource to the desired state for that property, as defined by the node's catalog. If Puppet successfully compares the states and the property is already in sync (in other words, if the current state is the desired state), Puppet moves on to the next resource without noting anything. Otherwise, it attempts some action and records an event, which appears in the report it sends to the Puppet master at the end of the run. These reports provide the data presented on the **Events** page in the [PE console](./console_accessing.html).

## Event types

There are six types of event that can occur when Puppet reviews each property in your system and attempts to make any needed changes. If a property is already in sync with its catalog, no event is recorded: no news is good news in the world of events.  

| Event | Description |
| ----- | ----------- |
| Failure | A property was out of sync; Puppet tried to make changes, but was unsuccessful. |
| Corrective change | Puppet found an inconsistency between the last applied catalog and a property's configuration, and corrected the property to match the catalog. |
| Intentional change | Puppet applied catalog changes to a property. |
| Corrective no-op | Puppet found an inconsistency between the last applied catalog and a property's configuration, but Puppet was instructed to not make changes on this resource, via either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter. Instead of making a corrective change, Puppet logs a corrective no-op event and reports the change it would have made. | 
| Intentional no-op | Puppet would have applied catalog changes to a property., but Puppet was instructed to not make changes on this resource, via either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter. Instead of making an intentional change, Puppet logs an intentional no-op event and reports the change it would have made. |
| Skip | A prerequisite for this resource was not met, so Puppet did not compare its current state to the desired state. This prerequisite is either one of the resource's dependencies or a timing limitation set with [the `schedule` metaparameter]({{puppet}}/metaparameter.html#schedule). The resource might be in sync or out of sync; Puppet doesn't know yet. | 

> **Note:** If the `schedule` metaparameter is set for a given resource, and the scheduled time hasn't arrived when the run happens, that resource logs a skip event on the Events page. This is true for a user-defined `schedule`, but does not apply to built-in scheduled tasks that happen weekly, daily, or at other intervals.

## Working with the Events page

During times when your PE deployment is in a state of stability, with no changes being made by staff and everything functioning optimally, the Events page will report little activity, and might not seem terribly interesting. But when things begin to change---when packages require upgrades, when security concerns threaten, or when systems fail---the Events page will help you understand what's happening and where so you can quickly solve the problem.  

The Events page fetches data when loading, and does not refresh---even if there's a Puppet run while you're on the page---until you close or reload the page. This ensures that shifting data won't disrupt an investigation. 

You can see how recent the shown data is by checking the timestamp at the top of the page. Reload the page to update the data to the most recent events.

> **Note:** Keeping time synchronized across your deployment helps the Events page produce accurate information. Consider running [NTP](./quick_start_ntp.html) across your deployment. As a bonus, NTP is easily managed with PE, and setting it up is an excellent way to learn Puppet and PE.

### Monitoring infrastructure with the events summary

The Events page displays all events from the latest report of every responsive node in the deployment. (By default, PE considers a node unresponsive after one hour, but you can [configure this setting](./console_config.html#changing-the-unresponsive-node-timeframe) to meet your needs.)

The left pane of the Events page shows the events summary. This list is an overview of Puppet activity across your infrastructure, and can help you rapidly assess the magnitude of any issue.

The events summary is split into three categories---Classes, Nodes, and Resources---to help you investigate how a change or failure event impacts your entire deployment. The summary view shows the number of events for each category in per-event-type counts. 

![events][events]

[events]: ./images/console/cm_events.png

### Gaining insight with the detail view

Clicking an item in the summary view loads its details (and any sub-items) in the detail view on the right. The context pane on the left always shows the list of items from which the one in the right pane was chosen, to let you easily view similar items and compare their states.

Click any item in the sub-lists --- classes with failures, for example, or nodes with all event types --- to load more specific info into the detail pane and begin looking for the causes of notable events. Switch between perspectives to find the common threads among a group of failures or corrective changes, and follow them to a root cause.  

### Analyzing changes and failures 

You can use the Events page to analyze the root causes of events resulting from a Puppet run. For example, to understand the cause of a failure after a Puppet run, select the class, node, or resource with a failure in the summary view on the left, and then on the right, review the details of the failure. This way, you can find out what is failing, as in the following image, which lists failed resources on the left, and one resource selected and its failures displayed in the right pane:

![events_corrective][events_corrective]

[events_corrective]: ./images/console/cm_events_corrective.png

You can view additional details by clicking on the failed item in the detail pane. The detail pane now shows the failure, as well as the specifics of the failure, including the config version associated with the run and the specific line of code and manifest where the error occured.

![events_detail][events_detail]

[events_detail]: ./images/console/cm_events_detail.png

You can find out from the error what caused the failure, such as the manifest trying to set an owner of a file resource to a non-existent user on the intended platform. Then, you know the cause of the failure and which line of which manifest you need to edit to resolve the issue.

Use the class, node, and resource views to focus on the information you need. For example, if you’re concerned about a failed service, say Apache or MongoDB, you can start by looking into failed resources or classes. If you’re experiencing a geographic outage, you might start by drilling into failed node events.

For another example, if there is an error in a class called `apache::directories`, it means there is a problem with the Apache web server, likely in the setup of some directories the Apache server needs to operate successfully. The resource specifies the exact directory that couldn't be created as defined in the Puppet manifest. By looking at the problem in different perspectives, you can determine what needs to change to fix the problem, or you might discover that it's the directory itself that is the problem.

### Selecting an event timeframe

Use the dropdown at the top of the Events page to focus on the most recent Puppet run, or to view events generated within the past five minutes, 30 minutes, 60 minutes, or 24 hours. 

### Understanding event display issues 

In some special cases, events are not displayed as expected on the Events page. These cases are often caused by the way that the console receives data from other parts of Puppet Enterprise, but sometimes are due to the way your Puppet code is interpreted by Puppet.    

**Runs that restart PuppetDB are not displayed**
If a given Puppet run restarts PuppetDB, Puppet is not able to submit a run report from that run to PuppetDB since PuppetDB is not available. Because the Events page relies on data from PuppetDB, and PuppetDB reports are not queued, the Events page does not display any events from that run. Note that in such cases, a run report *is* available on the **Reports** page. Having a Puppet run restart PuppetDB is an unlikely scenario, but one that could arise in cases where some change to, say, a parameter in the `puppetdb` class causes the `pe-puppetdb` service to restart. 

**Runs without a compiled catalog are not displayed**
If a run encounters a catastrophic failure where an error prevents a catalog from compiling, the Events page does not display any failures. This is because no events occurred. Information on the Events page is primarily concerned with events, not runs.

**Simplified display for some resource types**
For resource types that take the `ensure` property, such as user or file resource types, the Events page displays a single event when the resource is first created. This is because Puppet has changed only one property (`ensure`), which sets all the baseline properties of that resource at once. For example, all of the properties of a given user are created when the user is added, just as if the user was added manually. If a later PE run changes properties of that user resource, each individual property change is shown as a separate event.
