---
layout: default
title: "Analyze changes across Puppet runs"
canonical: "/pe/latest/CM_state_inspector.html"
---

The Events page is a reporting tool that enables you to investigate the current state of your infrastructure by drilling down into failures and other changes. Its focus is on correlating information and presenting it from multiple perspectives in order to reveal common causes behind related events. The Events page provides insight into how Puppet is managing configurations, and what's happening where when events occur.

The Events page lets you accomplish two important tasks: monitoring a summary of your infrastructure's activity, and analyzing the details of important changes and failures. The Events page lets you analyze events from the point of view of nodes, resources, and classes so you can reject noise and choose the context that best allows you to understand those events that concern you.

## Navigating events

The Events page displays two panes of data. Clicking an item on the left shows its details (and any sub-items) in the pane on the right. The context pane on the left always shows the list of items from which the one in the right pane was chosen, to let you easily view similar items and compare their states.

![events][events]

[events]: ./images/console/burnside_cm_events.png


> ### Note: Refreshing and time periods
>
> The Events page fetches data when loading, and does not refresh---even if there's a Puppet run while you're on the page---until you close or reload the page. This ensures that shifting data won't accidentally disrupt an investigation. As you drill down into events, data is loaded on demand for the information you want.
>
> You can see how old the current data is by checking the timestamp at the top of the page. Reload the page in your browser to update the data to the most recent events.
>
> You can also restrict the time period for which the Events page is reporting by using the drop-down time period menu. The Events page does not display events that happened more than 24 hours in the past.

To report on, share, and analyze the right-pane data in another tool, you can export it to a CSV file using the **Export data** button: ![export][export]

[export]: ./images/console/icon_csvdownload.png

### Events

An "event" is PE’s attempt to modify an individual property of a given resource. During a Puppet run, Puppet compares the current state of each property on each resource to the desired state for that property. If Puppet successfully compares the states and the property is already in sync (in other words, if the current state is the desired state), Puppet moves on to the next resource without noting anything. Otherwise, it attempts some action and records an event, which appears in the report it sends to the Puppet master at the end of the run. These reports provide the data presented on the Events page.

Events provides information about the following four possible events for nodes, classes, and resources:

* **Failure:** A property was out of sync; Puppet tried to make changes, but was unsuccessful.
* **Change:** A property was out of sync, and Puppet had to make changes to reach the desired state.
* **Skipped:** A prerequisite for this resource was not met, so Puppet did not compare its current state to the desired state. (This prerequisite is either a failure in one of the resource's dependencies or a timing limitation set with [the `schedule` metaparameter]({{puppet}}/metaparameter.html#schedule).) The resource may be in sync or out of sync; Puppet doesn't know yet.
* **No-op:** A property was out of sync, but Puppet was previously instructed to not make changes on this resource (using either the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter). Instead of making changes, Puppet logs a no-op event and reports the changes it would have made.

### Perspectives

The Events page provides three perspectives to correlate and contextualize information about events:

* Classes
* Nodes
* Resources

For example, if you’re concerned about a failed service, say Apache or MongoDB, you can start by looking into failed resources or classes. On the other hand, if you’re experiencing a geographic outage, you might start by drilling into failed node events.

Switching between perspectives can help you find the common threads among a group of failures, and follow them to a root cause. One way to think about this is to see the node as the physical location where an event takes place, while a class shows the particular part of the Puppet configuration that contained an event, and a resource provides further detail on what went wrong in that area. You might have an error in a class called `apache::directories`, which tells you there was a problem with the Apache web server, likely in the setup of some directories the Apache server needs to operate successfully. The resource specifies the exact directory that couldn't be created as defined in the Puppet manifest. That way, you can usually determine what needs to change to fix the problem, or you might discover that it's the directory itself that is the problem.

### Summary view: Monitoring infrastructure

On the Events page, the left pane contains the **summary view.** This list is an overview of recent Puppet activity across your whole infrastructure, and can help you rapidly assess the magnitude of any issue.

The summary view is split into three sub-lists: Classes, Nodes, and Resources. Each sub-list shows the number of events for that perspective, both as per-event-type counts and as bar graphs that measure against the total event count from that perspective. For example, if four classes have events, and two of those classes have events that are failures, the classes' **with failures** bar graph is at 50%.

Click any item in the sub-lists --- classes with failures, nodes with events, for example --- to load more specific info into the detail pane and begin looking for the causes of notable events. Until an item is selected, the right pane defaults to showing classes with failures.

### Analyzing changes and failures

You can use the Events page to analyze the root causes of events resulting from a Puppet run, as listed in the page's summary view. Instead of taking a node-centric perspective on a deployment, the Events page takes a more holistic approach by adding the class and resource views as well.

For example, to understand the cause of a failure after a Puppet run, you can select the class, node, or resource with a failure in the left pane, and then on the right, review the details of the failure. This way, you can find out what is failing, as in the following image, which lists failed resources on the left, and one resource selected and its failures displayed in the right pane:

![failed_resources][failed_resources]

[failed_resources]: ./images/console/burnside_cm_events_failedresources.png

You can drill down further by clicking on the failed item in the detail pane. The detail pane now shows the failure, as well as the specifics of the failure, including the config version associated with the run and the specific line of code and manifest where the error occured.

![failed_resources_detail][failed_resources_detail]

[failed_resources_detail]: ./images/console/burnside_cm_events_failure_drill_down.png

You can find out from the error what caused the failure, such as the manifest trying to set an owner of a file resource to a non-existent user on the intended platform. Then, you know the cause of the failure and which line of which manifest you need to edit to resolve the issue.

### Tips and issues

#### Runs that restart PuppetDB are not displayed

If a given Puppet run restarts PuppetDB, Puppet is not able to submit a run report from that run to PuppetDB since PuppetDB is not available. Because the Events page relies on data from PuppetDB, and PuppetDB reports are not queued, the Events page does not display any events from that run. Note that in such cases, a run report *is* available on the **Reports** page. Having a Puppet run restart PuppetDB is an unlikely scenario, but one that could arise in cases where some change to, say, a parameter in the `puppetdb` class causes the `pe-puppetdb` service to restart. This is a known issue that will be fixed in a future release.

#### Runs without events are not displayed

If a run encounters a catastrophic failure where an error prevents a catalog from compiling, the Events page does not display any failures. This is because no events actually occurred. It's important to remember that information on the Events page is primarily concerned with events, not runs.

#### Time sync is important

Keeping time synchronized across your deployment helps the Events page produce accurate information and keep it running smoothly. Consider running [NTP](./quick_start_ntp.html) across your deployment. As a bonus, NTP is easily managed with PE, and doing so is an excellent way to learn Puppet and PE if you are new to them.

#### Scheduled resources log skips

If the `schedule` metaparameter is set for a given resource, and the scheduled time has not yet arrived, that resource logs a "skip" event on the Events page. Note that this is only true for user-defined `schedule` and does not apply to built-in scheduled tasks that happen weekly, daily, or at other intervals. 

#### Simplified display for some resource types

For resource types that take the `ensure` property, (such as user or file resource types), when the resource is first created, the Events page only displays a single event. This is because Puppet has only changed one property (`ensure`), which sets all the baseline properties of that resource at once. For example, all of the properties of a given user are created when the user is added, just as if the user was added manually. If a PE run changes properties of that user resource later, each individual property change is shown as a separate event.
