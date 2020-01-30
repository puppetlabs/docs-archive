---
layout: default
title: "Set up Slack and email notifications"
---

You can set up email and Slack notifications for container builds and deployments. 

The following notification options are available:
* A container build starts
* A container build completes
* A deployment starts
* A deployment completes 

Failed builds or deployments are considered completed and give notifications with an "event failed" status.

Build notifications are specific to each container. You must set up separate notifications for each container that you want to be notified about. You can access build notifications from your project. 

Deployment notifications are specific to each cluster namespace. You must set up separate notifications for each namespace that you want to be notified about. You can access deployment notifications from your project, or through the **Clusters** page. 

> **Remember**: Before you set up Slack notifications, make sure you've enabled Slack in Integrations. 

**Related topics**:
* For more information about containers, see [Containers](./container.md).
* For more information about Kubernetes deployments, see [Set up a deployment](./deployment-set-up.md).
* For more information about integrations, see [Integrations](./integrate.md).