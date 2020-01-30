---
layout: default
title: "Updating a Pipelines Enterprise license"
--- 

Follow these steps to update your Pipelines Enterprise license.

Before you update your license:
* Make sure that you have access to your root administrator account.
* Download a new license from <a href="https://licenses.puppet.com" target="_blank">Puppet license manager</a>.

To update your account license:
1. Sign in with the root administrator account. 
1. Click **Console**.
1. Click **Settings**.
1. Under **Puppet Pipelines License**, click **Update**.
1. Click **Upload** and upload your new license file. 
1. Click **Submit License**. Your expiration date updates.
1. (Optional) If you still see the license expiration warning banner, restart the Pipelines application:
   1. List your running containers and find the container ID for `pipelines:latest`:
      
      ```docker ps```
      
   1. Restart the container:
      
      ```docker restart <YOUR-CONTAINER-ID>```

You've updated your license. You can now sign out of your root administrator account.
