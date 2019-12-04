---
layout: default
title: "Puppet Enterprise analytics data collection"
canonical: "/pe/latest/analytics_opt_out.html"
---

<!--Overview-->

Some components of Puppet Enterprise (PE) automatically collect data about how you use PE. 
If you want to opt out of providing this data, you can do so, either as part of an initial PE installation, or for existing installs.

<!--Concept--> 

## What data does PE collect?   

Puppet Enterprise collects the following data when Puppet Server starts or restarts, and every 24 hours thereafter:

* License UUID
* Number of licensed nodes
* Product name
* PE version
* Master operating system
* Master's public IP address
* Number of nodes in deployment
* Agent operating system(s)
* Number of agents running each operating system
* Puppet agent version(s)
* Number of agents running each version of Puppet agent

While in use, the PE console collects:

* Pageviews
* Link and button clicks
* Page load time
* User language
* Screen resolution
* Viewport size
* Anonymized IP address

The PE console **does not** collect:

* User inputs such as node or group names, user names, rules, parameters, or variables 

The collected data is tied to a unique, anonymized identifier for each Puppet master and your site as a whole. No personally identifiable information is collected, and the data we collect is never used or shared outside Puppet. 

### How does sharing this data benefit PE users?

We can, and have, used the data that companies share with us to quickly and efficiently identify organizations that could be affected by a security issue, alert them to the issue, and provide them with steps to take or fixes to download. In addition, data like this helps us understand how you use the product, which in turn helps us improve the product to meet your needs. 

### How does Puppet use the collected data?

The data we collect provides just one of many methods we use for learning about our customers. The more we know, the better we can address your needs. For example, knowing how many nodes you manage helps us develop more realistic product testing. And learning which operating systems are the most and the least used helps us decide where to prioritize new functionality. By collecting data, we begin to understand you better as a customer instead of as a data point.

<!--Multitask-->

## Opt out of Puppet Enterprise analytics data collection

If you choose to opt out of analytics data collection, you can do so during the installation process, or at any time after installation is complete. 

### Opt out during the installation process 

In order to opt out of Puppet's data collection during a fresh installation, you must install using the [text-mode installation]({{pe}}/install_text_mode.html). The text-mode installation uses the pe.conf file to set parameters and values needed for the install.

If you prefer to use an installation method other than text mode, first complete your installation, and then follow the steps to opt out of an existing PE installation.  

1. Follow the instructions for a [monolithic text-mode install]({{pe}}/install_text_mode_mono.html), or a [split text-mode install]({{pe}}/install_text_mode_split.html). At the step that asks you to "select 'text-mode' when prompted," and then begin to edit `pe.conf`,  set the  `puppet_enterprise::send_analytics_data` variable to false, as follows:

    `"puppet_enterprise::send_analytics_data": false`

2. Follow the rest of the instructions as described. By running Puppet with this setting, you will be "opted out" of data collection.

### Opt out of an existing PE installation

If you've already installed PE and want to disable data collection, follow these steps.

1. In the PE console, click **Classification**, and then click **PE Infrastructure**. 

2. On the **Classes** tab, on the `puppet_enterprise` class, add the parameter, `send_analytics_data` and set the **Value** to `false`.
 By default, this variable is set to `true`.
 
3. Run Puppet to enforce the change. On the **Inventory** page, click your master node and then click **Run Puppet**. Do the same for your console node. You can also run the `puppet job` command. For example: 

    `$ puppet job run --nodes puppetmaster.domain.com,puppetconsole.domain.com`

    Either option will enforce the changes required to opt out of data collection.

4. If you are using PE 2016.5.1, reload pe-console-services and Puppet Server by running:

     `sudo service pe-console-services reload`
     and
     `sudo service pe-puppetserver reload`
     
     This step is unnecessary for users of PE 2016.5.2.
