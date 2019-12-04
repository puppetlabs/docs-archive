---
layout: default
title: "Configuring and tuning your Puppet Enterprise infrastructure"
canonical: "/pe/latest/config_intro.html"
---

After you've installed Puppet Enterprise (PE), optimize it for your environment by configuring and tuning settings as needed. For example, you might want to add your own certificate to the whitelist, increase the max-threads setting for `http` and `https` requests, or configure the number of JRuby instances.

There are two main methods for configuring PE: by using the PE console or by adding a key to Hiera (this latter approach is sometimes referred to as a Hiera override).

In general, use the console to apply and configure `profile` classes starting with `puppet_enterprise::profile` or when editing or adding any parameters in PE-managed configuration files. Parameter values set in the PE console will override those you've manually set in the configuration files.

Use Hiera to apply and configure classes that aren't profile classes.

## Configure settings using the console

In general, use the console to apply and configure `profile` classes starting with `puppet_enterprise::profile` or when editing or adding any parameters in PE-managed configuration files. An example of how to configure settings in the console by attaching your own certificate follows.

>**Before you begin**: Review the information on [preconfigured node groups](./console_classes_groups_preconfigured_groups.html). You can badly damage your PE installation if you remove some classes, which are detailed on that page.

1. In the console, click **Nodes** > **Classification**, and select the node group that contains the class you want to work with. In the **PE Infrastructure** node group, select the **PE Certificate Authority** group

2. On the **Classes** tab, find the class you want to work with, select the **Parameter name** from the list and edit its value:

    <table>
      <tr>
        <th>Class</th>
        <th>Parameter</th>
        <th>Value</th>
      </tr>
      <tr>
        <td><code>puppet_enterprise::profile::certificate_authority</code></td>
        <td><code>client_whitelist</code></td>
        <td><code>example.puppetlabs.vm</code></td>
      </tr>
    </table>

3. Click **Add parameter**, and commit changes.

## Configure settings with Hiera

Use Hiera to apply and configure classes that aren't `profile` classes.

For example, you can use Hiera to [increase or decrease the number of JRuby instances on the Puppet Server](./config_puppetserver.html#tuning-jruby-on-puppet-server), or use it to [tune the number of `max_requests_per_instance` that will accept requests on the Puppet Server](./config_puppetserver.html#tuning-max-requests-per-instance-on-puppet-server).

>**Before you begin**: Review the information on [preconfigured node groups](./console_classes_groups_preconfigured_groups.html). You can badly damage your PE installation if you remove some classes, which are detailed on that page.

1. In your default Hiera .yaml file, add the setting you're configuring. The default location for the Hiera defaults is `/etc/puppetlabs/code/environments/%{environment}/hieradata` (for *nix) and `%CommonAppData%\PuppetLabs\code\environments\%{environment}\hieradata` (for Windows). If you've customized your `hierarchy` or `datadir`, you'll need to access and edit the default `.yaml` file accordingly.

************
**Related links**

Read more about settings you might want to tune on PE components:

- [Configuring and tuning Puppet Server](./config_puppetserver.html)

- [Configuring and tuning PuppetDB](./config_puppetdb.html)

- [Configuring and tuning node classifier and the console](./console_config.html)

- [Configuring and tuning orchestration](./config_orchestration.html)

- [Configuring Java arguments for PE](./config_java_args.html)