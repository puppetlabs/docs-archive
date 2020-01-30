---
layout: default
title: "Notifications"
---

You can receive notification when Pipelines for Applications initiates or completes an event such as a build or a deploy. Pipelines for Applications integrates with Slack and HipChat, and can also be configured to send notification emails to you and your team. 

## Notification types

Notifications in Pipelines allow you to know when manual and automated tasks occur. You can set up multiple notifications using any or all of the following notification types:

* **Email** notifications send you an email when the notification event occurs. You can set up multiple email notifications.
* **Team** notifications email every member of the team when the notification event occurs. You can set up multiple team notifications.
* **HipChat** notifications send an instant message to a HipChat room when the notification event occurs. You can set up multiple HipChat notifications. See [Integrate HipChat](./integrate-hipchat.html) for instructions on how to set up the integration. 
* **Slack** notifications send an instant message to a Slack channel when the notification event occurs. You can set up multiple Slack notifications. See [Integrate Slack](./integrate-slack.html) for instructions on how to set up the integration.

> **Tip:** You can also create custom webhooks that are triggered when a build or deploy is started or completes. See the [Webhooks](./webhook.html) documentation to learn more.

## Creating a "build begins" notification

When a build of an application begins, a notification event occurs. This event can trigger a customized notification that you can configure. This event occurs whether the build was triggered manually or automatically. This event occurs whether the build is on Pipelines servers or on your server(s).

Build notifications are <i>application</i> specific. Build notifications can be found in application settings. 

> **Before you begin:** Ensure you are using the correct team or individual account.

1. Navigate to the application for which you wish to receive build notifications.
1. Click **Application Settings**.
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. Select **Send a notification when a build of this application begins**.
1. In the **Add a notification:** section, select email, [Slack](./integrate-slack.html), or [HipChat](./integrate-hipchat.html).
1. Enter or select the email address, HipChat room, or Slack channel where you wish to receive notifications. 
1. Click **Add Notification**. 

Repeat this process to add additional "build begins" notifications. 

## Creating a "build completes" notification

When a build of an application is done, a notification event occurs. This event can trigger a customized notification that you can configure. This event occurs whether the build was triggered manually or automatically. This event occurs whether the build is on Pipelines servers or your own.

Build notifications are <i>application</i> specific. Build notifications can be found in application settings. 

> **Before you begin:** Ensure you are using the correct team or individual account.

1. Navigate to the application for which you wish to receive build notifications.
1. Click **Application Settings**.
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. Select **Send a notification when a build of this application completes**.
1. In the **Add a notification:** section, select email, [Slack](./integrate-slack.html), or [HipChat](./integrate-hipchat.html).
1. Enter or select the email address, HipChat room, or Slack channel where you wish to receive notifications. 
1. Click **Add Notification**. 

Repeat this process to add additional "build completes" notifications. 

## Deleting a build notification

1. Navigate to the application for which you wish to stop receiving notifications.
1. Click **Application Settings**.
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. In the **Existing Notifications:** list, find the notification you wish to delete and click the red x. 

## Creating a "deploy begins" notification

When a deploy of an application begins, a notification event occurs. This event can trigger a customized notification that you can configure. This event occurs whether the deploy was triggered manually or automatically.

Deploy notifications are <i>application environment</i> specific. Deploy notifications can be found in application environment settings. 

> **Before you begin:** Ensure you are using the correct team or individual account.

1. 1. Navigate to the application for which you wish to receive deploy notifications.
1. Click **Environments**.
1. Select the environment for which you wish to receive deploy notifications.
1. Click **Environment Settings**. 
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. Select **Send a notification when a deployment to this environment begins**. 
1. In the **Medium** section, select email, [Slack](./integrate-slack.html), or [HipChat](./integrate-hipchat.html).
1. Enter or select the email address, HipChat room, or Slack channel where you wish to receive notifications. 
1. Click **Add Notification**. 

Repeat this process to add additional "deploy begins" notifications. 

## Creating a "deploy ends" notification

When a deploy of an application begins, a notification event occurs. This event can trigger a customized notification that you can configure. This event occurs whether the deploy was triggered manually or automatically.

Deploy notifications are <i>application environment</i> specific. Deploy notifications can be found in application environment settings. 

> **Before you begin:** Ensure you are using the correct team or individual account.

1. 1. Navigate to the application for which you wish to receive deploy notifications.
1. Click **Environments**.
1. Select the environment for which you wish to receive deploy notifications.
1. Click **Environment Settings**. 
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. Select **Send a notification when a deployment to this environment ends**. 
1. In the **Medium** section, select email, [Slack](./integrate-slack.html), or [HipChat](./integrate-hipchat.html).
1. Enter or select the email address, HipChat room, or Slack channel where you wish to receive notifications. 
1. Click **Add Notification**. 

Repeat this process to add additional "deploy ends" notifications. 

## Deleting a deploy notification

1. 1. Navigate to the application for which you wish to receive deploy notifications.
1. Click **Environments**.
1. Select the environment for which you wish to receive deploy notifications.
1. Click **Environment Settings**. 
1. Expand the **Notification** section by clicking the expand button at the right of the screen.
1. In the **Existing Notifications:** list, find the notification you wish to delete and click the garbage can icon. 




