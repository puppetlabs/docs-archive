---
layout: default
title: "Monitor PE services"
canonical: "/pe/latest/CM_status.html"
---

Puppet Enterprise includes tools for monitoring the status of core PE services, including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB. You can monitor these services on the command line, and from within the PE console.

## View the Puppet Services status monitor

The Puppet Services status monitor provides a visual overview of the current state of key Puppet services, and can be used to quickly determine whether an unresponsive or restarting service is causing an issue with your Puppet deployment.  

1. Navigate to the **Overview** page in the PE console. 
2. Click **Puppet Services status** to open the monitor.
   A checkmark appears next to **Puppet Services status** if all applicable services are accepting requests. In the event that no data is available, a question mark appears next to the link. If one or more services is restarting or not accepting requests, a warning icon appears.

## `puppet infrastructure status` command

The `puppet infrastructure status` command displays errors and alerts from PE services, including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB. If [high availability](./ha_overview.html) is enabled in your environment, the command reports separately on the primary master, primary master replica, and compile masters.


