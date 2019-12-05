---
layout: default
title: "SSH quick start guide"
canonical: "/pe/latest/quick_start_ssh.html"
---

[downloads]: http://info.puppetlabs.com/download-pe.html
[sys_req]: ./install_system_requirements.html
[agent_install]: ./install_agents.html
[install_overview]: ./install_basic.html

Welcome to the Puppet Enterprise SSH Quick Start Guide. This document provides instructions for getting started managing SSH across your PE deployment using a module from the Puppet Forge.

Secure Shell (SSH) is a protocol that enables encrypted connections between nodes on a network for administrative purposes. It is most commonly used in the *nix world by admins who wish to remotely log into machines to access the command line and execute commands and scripts.

Typically, the first time you attempt to SSH into a host you’ve never connected to before, you get a warning similar to the following:

    The authenticity of host '10.10.10.9 (10.10.10.9)' can't be established.
    RSA key fingerprint is 05:75:12:9a:64:2f:29:27:39:35:a6:92:2b:54:79:5f.
    Are you sure you want to continue connecting (yes/no)?

If you select yes, the public key for that host is added to your SSH `known_hosts` file, and you won’t have to authenticate it again unless that host’s key changes.

The SSH module you’ll install in this exercise uses Puppet resources that collect and distribute the public key for each agent node in your PE deployment, which will enable you to SSH to and from any node without authentication warnings.

Using this guide, you will:

* [Install the ghoneycutt-ssh module](#install-the-ghoneycutt-ssh-module).
* [Use the PE console to add classes from the SSH module to your agent nodes](#use-the-pe-console-to-add-classes-from-the-ssh-module).
* [Use the PE console Events page to view changes to your infrastructure made by the `ssh` class](#use-the-puppet-enterprise-console-events-page-to-view-changes-made-by-the-ssh-class).
* [Use the PE console to edit root login parameters of the `ssh` class](#use-the-pe-console-to-edit-root-login-parameters-of-the-ssh-class).


## Install Puppet Enterprise and the Puppet Enterprise agent

If you haven't already done so, install PE. See the [system requirements][sys_req] for supported platforms.

1. [Download and verify the appropriate tarball][downloads].
2. Refer to the [installation overview][install_overview] to determine how you want to install PE, and follow the instructions provided.
3. Refer to the [agent installation instructions][agent_install] to determine how you want to install your PE agents, and follow the instructions provided.


## Install the ghoneycutt-ssh module

The ghoneycutt-ssh module, available on the Puppet Forge, is one of many modules written by  members of our user community.

You can learn more about the ghoneycutt-ssh module by visiting [https://forge.puppet.com/ghoneycutt/ssh](https://forge.puppet.com/ghoneycutt/ssh).

**To install the ghoneycutt-ssh module**:

From the PE master, run `puppet module install ghoneycutt-ssh -v 3.40.0`.

You should see output similar to the following:

        Notice: Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from https://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └─┬ ghoneycutt-ssh (v3.40.0)
          ├── ghoneycutt-common (v1.6.0)
          ├── puppetlabs-firewall (v1.8.1)
          └── puppetlabs-stdlib (v4.9.1)

> That's it! You've just installed the ghoneycutt-ssh module. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

## Create SSH group

Groups let you assign classes and variables to many nodes at once. Nodes can belong to many groups and will inherit classes and variables from all of them. Groups can also be members of other groups and inherit configuration information from their parent group the same way nodes do. PE automatically creates several groups in the console, which you can read more about in the [PE docs](./console_classes_groups_preconfigured_groups.html).

In this procedure, you’ll create a simple group called __ssh_example__, which will contain all of your nodes. Depending on your needs or infrastructure, you might have a different group that you'll assign SSH to.

[ssh_add_node]: ./images/quick/ssh_add_node.png

**To create the ssh_example group**:

1. In the PE console, click **Nodes** > **Classification**, then click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **default**.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *ssh_example*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
3. Click **Add**.
4. Click the *ssh_example* group, then select the **Rules** tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **~** (matches regex).
7. In the **Value** field, enter `.\*`.
8. Click **Add rule**.

   This rule will ["dynamically" pin all nodes](./console_classes_groups.html#adding-nodes-dynamically) to the **ssh_example** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment will vary from user to user.

## Use the PE console to add classes from the SSH module

[classification_selector]: ./images/quick/classification_selector.png

The ghoneycutt-ssh module contains one **class**. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes.

You're going to add the `ssh` class to the **ssh_example** node group. Depending on your needs or infrastructure, you may have a different group that you'll assign SSH to, but these same instructions would apply.

After you apply the `ssh` class and run Puppet, the public key for each agent node will be exported and then disseminated to the known_hosts files of the other agent nodes in the group, and you will no longer be asked to authenticate those nodes on future SSH attempts.

**To add the** `ssh` **class to the ssh_example group**:

1. In the console, click __Nodes__ in the side navigation bar.

2. On the __Classification__ page, select the __ssh_example__ group.

3. Click the __Classes__ tab.

4. In the __Class name__ field, begin typing `ssh`, and select it from the autocomplete list.

5. Click __Add class__.

6. Click __Commit 1 change__.

   **Note**: The `ssh` class now appears in the list of classes for the __ssh_example__ group, but it has not yet been configured on your nodes. For that to happen, kick off a Puppet run.

7. From the command line of your Puppet master, run `puppet agent -t`.

8. From the command line of each PE-managed node, run `puppet agent -t`.

   This will configure the nodes using the newly-assigned classes. Wait one or two minutes.

   **Important**: You need to run Puppet a second time due to the round-robin nature of the key sharing. In other words, the first server that ran on the first Puppet run was only able to share its key, but it was not also able to retrieve the keys from the other agents. It will collect the other keys on the second Puppet run.

## Use the Puppet Enterprise console events page to view changes made by the `ssh` class

The **Events** page lets you view and research changes and other events. For example, after applying the `ssh` class, you can use the **Events** page to confirm that changes were indeed made to your infrastructure.

Note that in the summary pane on the left, one event, a successful change, has been recorded for **Classes: with events**. However, there are three changes for **Classes: with events** and six changes **Resources: with events**.

Click __With Changes__ in the __Classes: with events__ summary view. The main pane will show you that the `Ssh` class was successfully added when you ran PE. This class set the `known_hosts` entries after it collects the public keys from agents nodes in your deployment .

Click __Changed__ in the __Resources: with events__ summary view. The main page will show you that public key resources for each agent in our example has now been brought under PE management. The further you drill down, the more information you’ll receive about the event. For example, in this case, you see that the the SSH rsa key for __agent1.example.com__ has been created and is now present in the `known_hosts` file for __master.example.com__.

If there had been a problem applying any piece of the `ssh` class, the information found here could tell you exactly which piece of code you need to fix. In this case, the **Events** page simply lets you confirm that PE is now managing SSH keys.

In the upper right corner of the detail pane is a link to a run report which contains information about the Puppet run that made the change, including logs and metrics about the run. See [Infrastructure reports](./CM_reports.html) for more information.

For more information about using the **Events** page, see [Working with the Events page](./CM_events.html##working-with-the-events-page).


## Use the PE console to edit root login parameters of the `ssh` class

With Puppet Enterprise you can edit or add class parameters in the PE console without needing to edit the module code directly.

The ghoneycutt-ssh module, by default, allows root login over SSH. But what if your compliance protocols do not allow this on certain pools of nodes?

Changing this parameter of the `ssh` class can be accomplished in a few steps using the PE console.

**To edit the root login parameter of the** `ssh` **class**:

1. In the console, click __Nodes__ in the navigation bar.
2. On the __Classification__ page, select the __ssh_example__ group.
3. Click the __Classes__ tab, and find `ssh` in the list of classes.

4. From the __parameter__ drop-down menu, choose __permit\_root\_login__.

   **Note**: The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by clicking __Discard changes__ after you have added the parameter.

5. In the __Value__ field, enter `no`.
6. Click __Add parameter__.
7. Click __Commit 1 change__.
8. From the command line of your Puppet master, run `puppet agent -t`.
9. From the command line of each PE-managed node, run `puppet agent -t`.

   This will trigger a Puppet run to have Puppet Enterprise create the new configuration.

10. Attempt to SSH from one agent to another. Note that root login permissions are now denied over SSH.

> Puppet Enterprise is now managing the root login parameter for your SSH configuration. You can see this setting in `/etc/ssh/sshd_config`. For fun, change the `PermitRootLogin` parameter to `yes`, run PE, and then recheck this file. As long as the parameter is set to `no` in the PE console, the parameter in this file will be set back to `no` on every Puppet run if it is ever changed.
>
> You can use the PE console to manage other SSH parameters, such as agent forwarding, X11 forwarding, and password authentication.


## Other Resources

For a video on automating SSH with Puppet Enterprise, check out [Automate SSH configuration in 5 minutes with Puppet Enterprise](http://puppetlabs.com/resources/video/automate-ssh).

[Speed up SSH by reusing connections](https://puppetlabs.com/blog/speed-up-ssh-by-reusing-connections) on the Puppet blog gives some helpful hints for working with SSH.

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the [learning Puppet page](https://puppetlabs.com/learn) to discover more.

* [Learning Puppet](/learning/) is a series of exercises on various core topics about deploying and using PE.
* The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the [learning page](https://puppetlabs.com/learn).


