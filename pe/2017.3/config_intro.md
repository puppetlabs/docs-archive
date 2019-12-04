# Configuring and tuning your Puppet Enterprise infrastructure

After you've installed Puppet Enterprise \(PE\), optimize it for your environment by configuring and tuning settings as needed. For example, you might want to add your own certificate to the whitelist, increase the max-threads setting for `http` and `https` requests, or configure the number of JRuby instances.

There are two main methods for configuring PE: by using the console or by adding a key to Hiera \(this latter approach is sometimes referred to as a Hiera override\).

In general, use the console to apply and configure `profile` classes starting with `puppet_enterprise::profile` or when editing or adding any parameters in PE-managed configuration files. Parameter values set in the console will override those you've manually set in the configuration files.

Use Hiera to apply and configure classes that aren't profile classes.

**Note:** If you are using multiple external data sources, the values you set in the console will override the settings in both Hiera and pe.conf. Settings in pe.conf take precedence over those in common.conf. Do not edit or add any additional settings to common.conf. Instead add any new settings to Hiera or the PE console as you did before.

**Related topics**  


[Configuring and tuning Puppet Server](config_puppetserver.md#)

[Configuring and tuning PuppetDB](config_puppetdb.md#)

[Configuring and tuning the console](config_console.md#)

[Configuring and tuning orchestration](config_orchestration.md#)

[Configuring Java arguments for Puppet Enterprise](config_java_args.md#)

## Configure settings using the console

In general, use the console to apply and configure `profile` classes starting with `puppet_enterprise::profile` or when editing or adding any parameters in PE-managed configuration files.

### Before you begin

Review the information on preconfigured node groups. You can badly damage your PE installation if you remove some classes, which are detailed on that page.

### Procedure

1.  In the console, click **Classification**, and select the node group that contains the class you want to work with.

2.  On the **Configuration** tab, find the class you want to work with, select the **Parameter name** from the list and edit its value, for example:

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise::profile::certificate_authority`|`client_whitelist`|`example.puppetlabs.vm`|

3.  Click **Add parameter** and commit changes.


**Related topics**  


[Preconfigured node groups](preconfigured_node_groups.md#)

## Configure settings with Hiera

Use Hiera to apply and configure classes that aren't `profile` classes.

### Before you begin

Review the information on preconfigured node groups. You can badly damage your PE installation if you remove some classes, which are detailed on that page.

### About this task

You can use Hiera to do things like increase or decrease the number of JRuby instances on the Puppet Server, or tune the number of `max_requests_per_instance` that will accept requests on the Puppet Server.

### Procedure

1.  In your Hiera default `.yaml` file, add the setting you're configuring.

    The location for the Hiera defaults is `/etc/puppetlabs/code/environments/%{environment}/hieradata` for \*nix and `%CommonAppData%\PuppetLabs\code\environments\%{environment}\hieradata` for Windows If you customized your `hierarchy` or `datadir`, access and edit the default `.yaml` file accordingly.


**Related topics**  


[Preconfigured node groups](preconfigured_node_groups.md#)

