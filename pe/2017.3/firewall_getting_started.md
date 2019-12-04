# Managing firewalls with PE

Follow the steps in this guide to get started managing firewall rules with the puppet-firewall module and a simple module you’ll write that defines those rules.

## Managing firewalls overview

Firewalls consist of a set of rules and policies for managing access.

With a firewall, admins define a set of policies \(firewall rules\) that usually consist of things like application ports \(TCP/UDP\), node interfaces \(which network port\), IP addresses, and an accept/deny statement. These rules are applied from a "top-to-bottom" approach.

For example, when a service, such as SSH, attempts to access resources on the other side of a firewall, the firewall applies a list of rules to determine if or how SSH communications are handled. If a rule allowing SSH access can’t be found, the firewall denies access to that SSH attempt.

To best manage firewall rules with PE, separate these rules into `pre` and `post` groups.

## Learning to manage firewalls with PE

To manage your firewall rules with Puppet Enterprise, define a group of nodes that you want to manage, and then use modules to manage the rules on those nodes.

Groups let you assign classes and variables to many nodes at once. Nodes can belong to many groups and inherit classes and variables from all of them. Groups can also be members of other groups and inherit configuration information from their parent group the same way nodes do. PE automatically creates several groups in the console.

To set up firewall management with Puppet Enterprise, you will:

-   Write a simple module to define the firewall rules for your PE-managed infrastructure.
-   Create a firewall node group
-   Add the `my_firewall` class to your agent nodes.
-   Write an additional class to open ports for the Puppet master
-   Enforce the desired state of the `my_firewall` class.

These instructions assume that you have installed PE. Refer to the installation overview and the agent installation instructions for complete instructions. See the supported operating system documentation for supported platforms. This guide assumes you are *not* using Code Manager or r10k.

**Tip:** Follow the instructions in the NTP getting started guide to have PE ensure time is in sync across your deployment.

Related topics:

-   Preconfigured groups in the console.

## About module directories

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.

PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.

There are plenty of resources about modules and the creation of modules that you can reference.

Related topics:

-   Puppet: Module fundamentals.
-   Puppet: The modulepath.
-   The Beginner's guide to modules.
-   The Puppet Forge.

## Install the `puppetlabs-firewall` module

Install the puppetlabs-firewall module to manage firewall policies.

### About this task

To get started managing and configuring firewall rules with Puppet, install the `puppetlabs-firewall` module.

### Procedure

1.  From the PE master, run `puppet module install puppetlabs-firewall`

    You should see output similar to the following:

    ``

    ```
    `Preparing to install into /etc/puppetlabs/puppet/environments/production/modules ...
    Notice: Downloading from https://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    /etc/puppetlabs/puppet/environments/production/modules
    └── puppetlabs-firewall (v1.6.0)`
    ```


### Results

That's it! You've just installed the `firewall` module. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

Related topics: Firewall

## Write the my\_firewall module

Create a simple firewall module.

### About this task

To define and help manage your firewall rules, you'll write a simple module containing just three classes.

For your module, you'll create the following files:

-   `my_firewall/`

    -   `manifests/`

        -   `pre.pp`

        -   `post.pp`

        -   `init.pp`


### Procedure

1.  From the command line on the Puppet master, navigate to the modules directory:

    `cd /etc/puppetlabs/code/environments/production/modules`

2.  Run `mkdir -p my_fw/manifests` to create the new module directory and its manifests directory.

3.  From the `manifests` directory, use your text editor to create `pre.pp`.

4.  Edit `pre.pp` so it contains the following Puppet code.

    ```
    class my_firewall::pre {
    
     # Default firewall rules
     firewall { '000 accept all icmp':
       proto   => 'icmp',
       action  => 'accept',
     }
    
     firewall { '001 accept all to lo interface':
       proto   => 'all',
       iniface => 'lo',
       action  => 'accept',
     }
    
     firewall { '002 accept related established rules':
       proto   => 'all',
       state   => ['RELATED', 'ESTABLISHED'],
       action  => 'accept',
     }
    
     # Allow SSH
     firewall { '100 allow ssh access':
       port   => '22',
       proto  => tcp,
       action => accept,
     }
    
    }
    ```

    The `pre.pp` file defines the "pre" group rules that the firewall applies when a service requests access.

5.  Save and exit the file.

6.  From the `manifests` directory, use your text editor to create `post.pp`.

7.  Edit `post.pp` so that it contains the following Puppet code.

    ```
    class my_firewall::post {
    
      firewall { "999 drop all other requests":
        action => "drop",
      }
    
    }
    ```

    The `post.pp` file defines the rule for the firewall to drop any requests that haven’t met the rules defined by `pre.pp`.

8.  Save and exit the file.

9.  From the `manifests` directory, use your text editor to create `init.pp`.

10. Edit the `init.pp` file so that it contains the following Puppet code.

    ```
     class my_firewall {
    
       stage { 'fw_pre':  before  => Stage['main']; }
       stage { 'fw_post': require => Stage['main']; }
    
       class { 'my_fw::pre':
         stage => 'fw_pre',
       }
    
       class { 'my_fw::post':
         stage => 'fw_post',
       }
    
      resources { "firewall":
         purge => true
      }
    
    }
    ```

    The `init.pp` file applies the previous two classes, as well as telling Puppet when to apply the classes in relation to the `main` stage \(which ensures the classes are applied in the correct order\).

11. Save and exit the file.


### Results

That's it! You've written a module that contains a class that, once applied, ensures your firewall has rules that will be managed by PE. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

## Create the firewall\_example group

Create a group of nodes that you want to manage firewalls for.

### About this task

To specify the nodes you want to manage firewalls on, create a group called `firewall_example`.

This group contains all of your nodes. Depending on your needs or infrastructure, you may have a different group that you assign your firewall class to.

### Procedure

1.  In the PE console, click **Nodes**\>** Classification**, and click **Add group**. 

2.  In the **Group name** field, name your group, for example firewall\_example, and click **Add**.

3.  Click **Add membership rules, classes, and variables**.

4.  On the **Rules** tab, in the **Node name** field, enter the name of the PE-managed node you want to add to this group, and click **Pin node**.

    Repeat this step for any additional nodes you want to add.

    CAUTION:

    Do not add the Puppet master to this group. Your firewall class does not yet contain rules to allow access to the Puppet master. You will add these rules later when you write a class to open ports for the Puppet master.

5.  Commit changes.


### Results

## Add the my\_firewall class to agent nodes

Enforce firewall rules by adding the firewall class to the nodes you want to manage.

### About this task

To define and enforce firewall rules across your agent notes, add the `my_firewall` class from your module to the node group.

### Procedure

1.  In the console, click **Classification**, and find and select the `firewall_example` group.

2.  On the **Configuration** tab, in the **Class name** field, select `my_firewall`.

    **Tip:** You only need to add the main `my_firewall` class. It contains the other classes from the module.

3.  Click **Add class**.

    The `my_firewall` class now appears in the list of classes for the `firewall_example` group.

    When Puppet runs, it configures the nodes using the newly-assigned classes. Wait one or two minutes.


### Results

Congratulations! You’ve just created a firewall class that you can use to define and enforce firewall rules across your PE-managed infrastructure.

## Open ports for the Puppet master

When creating firewall policies, you must allow special access to the Puppet master.

### About this task

To ensure that you can access the Puppet master correctly, you'll need to allow special firewall access. To do this, create a module that opens the ports for the Puppet master.

### Procedure

1.  From the command line on the Puppet master, navigate to the modules directory:

    `cd /etc/puppetlabs/puppet/modules`

2.  Run `mkdir -p privileges/manifests`to create the new module directory and its manifests directory.

3.  From the `manifests` directory, use your text editor to create the `init.pp` file, and edit it so it contains the following Puppet code.

    ```
    class my_master {
       include my_firewall
    
      firewall { '100 allow PE Console access':
        port   => '443',
        proto  => tcp,
        action => accept,
      }
    
      firewall { '100 allow Puppet master access':
        port   => '8140',
        proto  => tcp,
        action => accept,
      }
    
      firewall { '100 allow ActiveMQ MCollective access':
        port   => '61613',
        proto  => tcp,
        action => accept,
      }
    
     }
    ```

4.  Using the console, add `my_master` class to the Puppet master.

5.  Add the Puppet master to the `firewall_example` group.

6.  After Puppet runs, navigate to the **Reports** page, and click the latest run report for your Puppet master.

7.  Click the **Events** tab.

    You will see three events on this node, indicating that your firewall now allows access to MCollective, the PE console, and the Puppet master.

    **Tip:** For all firewall configuration needs for your PE installation, refer to the system configuration documentation.


## Check that PE enforces the desired state of the my\_firewall class

If your infrastructure changes from what you've specified, PE will correct that change.

### About this task

To test that PE enforces the desired state of the `my_firewall` class you created, make a manual change and then run Puppet.

For example, you applied the class `my_firewall` to define and enforce your firewall rules. If a member of your team changes the contents of the `iptables` to allow connections on a random port that is not specified in `my_firewall`, PE corrects the change the next time it runs. You can test this by making a manual change.

### Procedure

1.  Select an agent node on which you applied the `my_firewall` class, and run `iptables --list`.

2.  Note that the rules from the `my_firewall` class have been applied.

3.  From the command line, insert a new rule to allow connections to port 8449 by running `iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 8449 -j ACCEPT.`

4.  Run `iptables -- list` again and note this new rule is now listed.

5.  After Puppet runs on the agent node, run `iptables -- list` on that node once more, and notice that PE has enforced the desired state you specified for the firewall rules.


### Results

That's it --- PE has enforced the desired state of your agent node.

## Learning more about Puppet Enterprise and puppetlabs-firewall

The Puppet `firewall` module \(`puppetlabs-firewall`\), is part of the PE supported modules program; these modules are supported, tested, and maintained by Puppet.

You can learn more about the Puppet `firewall` module by visiting the Puppet Forge.

Check out the other getting started guides in our PE getting started guide series:

-   NTP getting started guide
-   SSH getting started guide
-   DNS getting started guide
-   Sudo users getting started guide

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the Learning Puppet page to discover more.

-   Learning Puppet is a series of exercises on various core topics about deploying and using PE.
-   The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the learning page.

