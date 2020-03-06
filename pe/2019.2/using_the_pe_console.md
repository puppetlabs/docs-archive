---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Restart a service using the console

The console is the graphical interface for Puppet Enterprise \(PE\).

Use the console to:

-   Manage node requests to join the Puppet deployment.
-   Assign Puppet classes to nodes and groups.
-   Run Puppet on specific groups of nodes.

-   View reports and activity graphs.
-   Run tasks and plans.
-   Browse and compare resources on your nodes.
-   View package and inventory data.
-   Manage console users and their access privileges.

You will learn how to accept the console certificate, log in, and run a task to restart a service.

There will be many more console functionalities covered later, which include things like managing permissions and creating node groups. These are taught in tandem with some system configuration tasks you might need to complete to get up and running.

Once you configure the basics and have added more nodes, roles, and users, we encourage you to revisit the console to get a deeper understanding of some of its core features.

## Reaching the console

The console is served as a website over SSL, on whichever port you chose when installing the console component.

Let's say your console server is `console.domain.com`. If you chose to use the default port \(443\), you can omit the port from the URL and reach the console by navigating to `https://console.domain.com`.

If you chose to use port 8443, you reach the console at `https://console.domain.com:8443`.

**Remember:** Always use the `https` protocol handler. You cannot reach the console over plain `http`.

## Accepting the console's certificate

The console uses an SSL certificate created by your own local Puppet certificate authority. Because this authority is specific to your site, web browsers won't know it or trust it, and you must add a security exception in order to access the console.

Adding a security exception for the console is safe to do. Your web browser warns you that the console's identity hasn't been verified by one of the external authorities it knows of, but that doesn't mean it's untrustworthy. Because you or another administrator at your site is in full control of which certificates the Puppet certificate authority signs, the authority verifying the site is *you.*

When your browser warns you that the certificate authority is invalid or unknown:

-   In Chrome, click **Advanced**, then **Proceed to <CONSOLE ADDRESS\>**.

-   In Firefox, click **Advanced**, then **Add exception**.

-   In Internet Explorer or Microsoft Edge, click **Continue to this website \(not recommended\)**.

-   In Safari, click **Continue**.


## Logging in

Accessing the console requires a username and password.

If you are an administrator setting up the console or accessing it for the first time, use the username and password you chose when you installed the console. Otherwise, get credentials from your site's administrator.

Because the console is the main point of control for your infrastructure, it is a good idea to prohibit your browser from storing the login credentials.

## Use a task to restart a service on one node

Puppet Enterprise allows you to run ad-hoc Bolt tasks using the console.

### Before you begin

Make sure you have installed PE and logged into the console.

### About this task

A task is a single action you can take on one or more targeted machines. It allows you to do things like upgrade packages and restart services without changing anything in your infrastructure. PE comes with a few tasks installed, such as `package`, `service`, and `puppet_conf`, but you can download more tasks from the Forge, which will be covered later.

In this section, you will learn how to access the tasks page in the console and run a job to execute the `service` task, which will instruct `PE` to restart the nginx service. It is important to be able to restart services when systems get hung up or when new applications are installed.

### Procedure

1.  In the console, in the Run section, click **Task**.

2.  In the **Task** field, select a task to run. For this example, use `service`.

    **Note:** If the task you want to run is not available, you either do not have it installed or do not have permission to run it.

3.  In the **Job description** field, provide a description. What you enter here appears on the job list and job details pages.

4.  Set parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    For the `service` task, you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

5.  Under **Schedule**, select **Now**.

    You can also schedule a task by selecting **Later** and setting a chosen time, day, and time zone.

6.  Under **Select targets**, choose **Node list** from the drop down.

    1.  In the **Inventory nodes** field, start typing the name of the node you want to target and click **Search** to find it.

7.  Once you have selected your target node, click **Run job** to execute the task.

8.  Navigate to the **Jobs** page to view the status of the job and any output it produces.


### Results

Congratulations! You have run a task using the PE console. To learn more about tasks, including how to install them from the Forge or how to write your own, visit the [Installing tasks](installing_tasks.md#) and [Writing tasks](writing_tasks.md#) section of the docs.

Next, install and manage the Apache server.

