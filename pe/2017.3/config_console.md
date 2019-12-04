# Configuring and tuning the console

After installing Puppet Enterprise, you can change product settings to customize the console's behavior, adjust to your team's needs, and improve performance.

**Related topics**  


[Disable update checking](config_puppetserver.md#)

[Configuring Java arguments for Puppet Enterprise](config_java_args.md#)

[Configure ulimit for PE services](config_ulimit.md#)

## Configure the PE console and console-services

To configure the behavior of the console and console-services, you can modify profile-based parameters from the console.

### About this task

**Note:** Use Hiera instead of these console steps to configure `puppet_enterprise::api_port` and `puppet_enterprise::console_services::no_longer_reporting_cutoff`.

### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.

2.  On the **Configuration** tab, locate the appropriate class, add parameters and values as needed, and commit changes.

3.  On the nodes hosting the master and console, run Puppet.


**Related topics**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

### Console and console-services parameters

Use these parameters to customize the behavior of the console and console-services. Parameters that begin with `puppet_enterprise::profile` can be modified from the console itself.

|Parameter|Value|Default|
|---------|-----|-------|
|`puppet_enterprise::profile::console::classifier_synchronization_period`|Integer representing, in seconds, the classifier synchronization period, which controls how long it takes the node classifier to retrieve classes from the master.|`"600"` seconds \(10 minutes\)|
|`puppet_enterprise::profile::console::rbac_session_timeout`|Integer representing, in minutes, how long a user's session may last. The session length is the same for node classification, RBAC, and the console.|`"60"`|
|`puppet_enterprise::profile::console::session_maximum_lifetime`|Integer representing the maximum allowable period that a console session may be valid. May be set to "0" to not expire before the maximum token lifetime. Supported units are "s" \(seconds\), "m" \(minutes\), "h" \(hours\), "d" \(days\), "y" \(years\). Units are specified as a single letter following an integer, for example "1d" \(1 day\). If no units are specified, the integer is treated as seconds.| |
|`puppet_enterprise::profile::console::console_ssl_listen_port`|Integer representing the port that the console is available on.|`"[443]"`|
|`puppet_enterprise::profile::console::ssl_listen_address`|Nginx listen address for the console|`"0.0.0.0"`|
|`puppet_enterprise::profile::console::classifier_prune_threshold`|Integer representing the number of days to wait before pruning the size of the classifier database. If you set the value to `"0"`, the node classifier service is never pruned.| |
|`puppet_enterprise::profile::console::classifier_node_check_in_storage`|`"true"` to store an explanation of how nodes match each group they're classified into, or `"false"`. |`"false"`|
|`puppet_enterprise::profile::console::display_local_time`|`"true"` to display timestamps in local time, with hover text showing UTC time, or `"false"` to show timestamps in UTC time.|`"false"`|

Modify these configuration parameters in Hiera, not the console: 

|Parameter|Value|Default|
|---------|-----|-------|
|`puppet_enterprise::api_port`|SSL port that the node classifier is served on.|`[4433]`|
|`puppet_enterprise::console_services::no_longer_reporting_cutoff`|Length of time, in seconds, before a node is considered unresponsive.|`3600` seconds|

## Change password reset and lockout settings

You can change the default settings for how long new password change tokens last, and how many failed login attempts are permitted.

### About this task

When a user doesn’t remember their current password, an administrator can generate a token for them to change their password. The duration, in hours, that this generated token is valid can be changed with the `password-reset-expiration` parameter. The default value is 24.

The `failed-attempts-lockout` parameter takes a positive integer that specifies how many failed login attempts are allowed on an account before that account is revoked. The default value is 10.

### Procedure

1.  On the node hosting the console, navigate to `/etc/puppetlabs/console-services/conf.d` and create a new file. The filename can be arbitrary, but the format of its contents is HOCON.

2.  In the file, add the following lines with the values you want:

    ```
    rbac: {
     password-reset-expiration: 24
     failed-attempts-lockout: 10
    }
    ```

3.  Reload pe-console-services by running `sudo service pe-console-services reload`


### Results

**Related topics**  


[Configuration file syntax](config_files.md#)

## Disable HTTPS redirect

The console redirects to HTTPS when you attempt to connect over HTTP. The pe-nginx webserver now listens on port 80 by default.

### About this task

The Hiera `.yaml` default location is `/etc/puppetlabs/code/environments/%{environment}/hieradata` \(for \*nix\) and `%CommonAppData%\PuppetLabs\code\environments\%{environment}\hieradata` \(for Windows\).

### Procedure

1.  Edit your Hiera `.yaml` file to add the following setting:

    ```
    puppet_enterprise::profile::console::proxy::http_redirect::enable_http_redirect: false
    ```


## Tuning the PostgreSQL buffer pool size

If you are experiencing performance issues or instability with the console, adjust the buffer memory settings for PostgreSQL.

### About this task

The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.

### Procedure

1.  Open the PE PostgreSQL configuration file, `/opt/puppetlabs/server/data/postgresql/9.6/data/postgresql.conf`.

2.  Change the `shared_buffers` setting so that it is about 25 percent of your hardware’s RAM.

3.  If you have a large or complex deployment, increase the `work_mem` value from the default of 1MB.

4.  Restart the PostgreSQL server by running: `sudo /etc/init.d/pe-postgresql restart`


### Results

For more detail, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/runtime-config-resource.html).

## Enable data editing in the console

The ability to edit configuration data in the console is enabled by default in new installations. If you upgrade from an earlier version and didn't previously have configuration data enabled, you must manually enable classifier configuration data, because enabling requires edits to your `hiera.yaml` file.

### About this task

### Procedure

1.  On your master, edit `/etc/puppetlabs/puppet/hiera.yaml` to add:

    ```
    hierarchy: 
    - name: "Classifier Configuration Data"
      data_hash: classifier_data        
    ```

    Place any additional hierarchy entries, such as `hiera-yaml` or `hiera-eyaml` under the same `hierarchy` key, preferably below the `Classifier Configuration Data` entry.


### Results

### What to do next

If your environment is configured for high availability, you must also update `hiera.yaml` on your replica.

