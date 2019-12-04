---
layout: default
title: "Analyze changes across Puppet runs"
canonical: "/pe/latest/CM_state_inspector.html"
---

An **event** is PE’s attempt to modify an individual property of a given resource. During a Puppet run, Puppet compares the current state of each property on each resource to the desired state for that property. If Puppet successfully compares the states and the property is already in sync (in other words, if the current state is the desired state), Puppet moves on to the next resource without noting anything. Otherwise, it attempts some action and records an event, which appears in the report it sends to the Puppet master at the end of the run. These reports provide the data presented on the **Events** page in the [PE console](./console_accessing.html).

The Events page lets you: 

* View a summary of activity in your infrastructure. 
* Analyze the details of important changes and failures.
* See common causes behind related events.

## Working with the Events page

The Events page provides information about four possible event types:

1. **With failures:** A property was out of sync; Puppet tried to make changes, but was unsuccessful.
1. **With changes:** A property was out of sync, and Puppet made changes to reach the desired state.
1. **With skips:** A prerequisite for this resource was not met, so Puppet did not compare its current state to the desired state. This prerequisite is either one of the resource's dependencies or a timing limitation set with [the `schedule` metaparameter]({{puppet}}/metaparameter.html#schedule). The resource might be in sync or out of sync; Puppet doesn't know yet.
1. **With no-op:** A property was out of sync, but Puppet was instructed to not make changes on this resource, via either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter. Instead of making changes, Puppet logs a no-op event and reports the changes it would have made.

> **Note:** Corrective changes are currently not reported on the Events page. Support for this feature will be added in a future release.  

### Selecting an event timeframe

Use the dropdown at the top of the Events page to focus on the most recent Puppet run, or to view events generated within the past five minutes, 30 minutes, 60 minutes, or 24 hours. 

> **Note: Refreshing and time periods**
>
> The Events page fetches data when loading, and does not refresh---even if there's a Puppet run while you're on the page---until you close or reload the page. This ensures that shifting data won't disrupt an investigation. As you drill down into events, data is loaded for the information you want.
>
> You can see how recent the shown data is by checking the timestamp at the top of the page. Reload the page to update the data to the most recent events.

### Monitoring infrastructure with the summary view

On the Events page, the left pane contains the **summary view**. This list is an overview of Puppet activity across your infrastructure, and can help you rapidly assess the magnitude of any issue.

The summary view is split into three categories: Classes, Nodes, and Resources: 

* A **class** shows the particular part of the Puppet configuration that contained an event.
* The **node** is the physical location where an event takes place.  
* A **resource** provides further detail on what went wrong in that area.

The summary view shows the number of events for each category, both as per-event-type counts and as bar graphs that measure against the total event count from that perspective. For example, if four classes have events, and two of those classes have events that are failures, the **Classes with failures** bar graph is at 50%. 

![events][events]

[events]: ./images/console/cm_events.png

### Gaining insight with the detail view

Clicking an item in the summary view loads its details (and any sub-items) in the detail view on the right. The context pane on the left always shows the list of items from which the one in the right pane was chosen, to let you easily view similar items and compare their states.

Click any item in the sub-lists --- classes with failures, for example, or nodes with events --- to load more specific info into the detail pane and begin looking for the causes of notable events. Switch between perspectives to find the common threads among a group of failures, and follow them to a root cause.  

### Analyzing changes and failures 

You can use the Events page to analyze the root causes of events resulting from a Puppet run. For example, to understand the cause of a failure after a Puppet run, select the class, node, or resource with a failure in the summary view on the left, and then on the right, review the details of the failure. This way, you can find out what is failing, as in the following image, which lists failed resources on the left, and one resource selected and its failures displayed in the right pane:

![failed_resources][failed_resources]

[failed_resources]: ./images/console/cm_events_failednode.png

You can view additional details by clicking on the failed item in the detail pane. The detail pane now shows the failure, as well as the specifics of the failure, including the config version associated with the run and the specific line of code and manifest where the error occured.

![failed_resources_detail][failed_resources_detail]

[failed_resources_detail]: ./images/console/cm_events_failure_drill_down.png

You can find out from the error what caused the failure, such as the manifest trying to set an owner of a file resource to a non-existent user on the intended platform. Then, you know the cause of the failure and which line of which manifest you need to edit to resolve the issue.

Use the class, node, and resource views to focus on the information you need. For example, if you’re concerned about a failed service, say Apache or MongoDB, you can start by looking into failed resources or classes. If you’re experiencing a geographic outage, you might start by drilling into failed node events.

For another example, if there is an error in a class called `apache::directories`, it means there is a problem with the Apache web server, likely in the setup of some directories the Apache server needs to operate successfully. The resource specifies the exact directory that couldn't be created as defined in the Puppet manifest. By looking at the problem in different perspectives, you can determine what needs to change to fix the problem, or you might discover that it's the directory itself that is the problem.

### Exporting events data to a CSV file

To report on, share, and analyze the data in another tool, export it to a CSV file by clicking **Export data**.

## Tips for working with the Events page

#### Customizing the unresponsive node timeframe

The Events page displays all events from the latest report of every responsive node in the deployment. By default, PE considers a node unresponsive after one hour. This timeframe can be configured in the `console.conf` file at `/etc/puppetlabs/console-services/conf.d` by adding `no-longer-reporting-cutoff` and expressing the desired timeframe in seconds:

		console: {
		...
  			no-longer-reporting-cutoff: 5400
		...
		}
 
#### Runs that restart PuppetDB are not displayed

If a given Puppet run restarts PuppetDB, Puppet is not able to submit a run report from that run to PuppetDB since PuppetDB is not available. Because the Events page relies on data from PuppetDB, and PuppetDB reports are not queued, the Events page does not display any events from that run. Note that in such cases, a run report *is* available on the **Reports** page. Having a Puppet run restart PuppetDB is an unlikely scenario, but one that could arise in cases where some change to, say, a parameter in the `puppetdb` class causes the `pe-puppetdb` service to restart. 

#### Runs without events are not displayed

If a run encounters a catastrophic failure where an error prevents a catalog from compiling, the Events page does not display any failures. This is because no events occurred. Information on the Events page is primarily concerned with events, not runs.

#### Time sync is important

Keeping time synchronized across your deployment helps the Events page produce accurate information. Consider running [NTP](./quick_start_ntp.html) across your deployment. As a bonus, NTP is easily managed with PE, and setting it up is an excellent way to learn Puppet and PE.

#### Scheduled resources log skips

If the `schedule` metaparameter is set for a given resource, and the scheduled time hasn't arrived when the run happens, that resource logs a skip event on the Events page. This is true for a user-defined `schedule`, but does not apply to built-in scheduled tasks that happen weekly, daily, or at other intervals.

#### Simplified display for some resource types

For resource types that take the `ensure` property, such as user or file resource types, the Events page displays a single event when the resource is first created. This is because Puppet has changed only one property (`ensure`), which sets all the baseline properties of that resource at once. For example, all of the properties of a given user are created when the user is added, just as if the user was added manually. If a later PE run changes properties of that user resource, each individual property change is shown as a separate event.
