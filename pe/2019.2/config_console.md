# Configuring and tuning the console

After installing Puppet Enterprise, you can change product settings to customize the console's behavior, adjust to your team's needs, and improve performance. Many settings can be configured in the console itself.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Disable update checking](config_puppetserver.md#)

[Configuring Java arguments](config_java_args.md#)

[Configure ulimit for PE services](config_ulimit.md#)

## Configure the PE console and console-services

Configure the behavior of the console and console-services, as needed.

### About this task

**Note:** Use the Hiera or `pe.conf` method to configure non-profile classes, such as `puppet_enterprise::api_port` and `puppet_enterprise::console_services::no_longer_reporting_cutoff`.

To configure settings in the console:

### Procedure

1.  Click **Classification**, and select the node group that contains the class you want to work with.

2.  On the **Configuration** tab, find the class you want to work with, select the **Parameter name** from the list and edit its value.

3.  Click **Add parameter** and commit changes.

4.  On the nodes hosting the master and console, run Puppet.


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

### Console and console-services parameters

Use these parameters to customize the behavior of the console and console-services. Parameters that begin with `puppet_enterprise::profile` can be modified from the console itself. See the configuration methods documents for more information on how to change parameters in the console or Hiera.

-   **`puppet_enterprise::profile::console::classifier_synchronization_period`**

    Integer representing, in seconds, the classifier synchronization period, which controls how long it takes the node classifier to retrieve classes from the master.

    Default: `"600"` \(seconds\).

-   **`puppet_enterprise::profile::console::rbac_failed_attempts_lockout`**

    Integer specifying how many failed login attempts are allowed on an account before that account is revoked.

    Default: `"10"` \(attempts\).

-   **`puppet_enterprise::profile::console::rbac_password_reset_expiration`**

    Integer representing, in hours, how long a user's generated token is valid for. An administrator generates this token for a user so that they can reset their password.

    Default: `"24"` \(hours\).

-   **`puppet_enterprise::profile::console::rbac_session_timeout`**

    Integer representing, in minutes, how long a user's session can last. The session length is the same for node classification, RBAC, and the console.

    Default: `"60"` \(minutes\).

-   **`puppet_enterprise::profile::console::session_maximum_lifetime`**

    Integer representing the maximum allowable period that a console session can be valid. To not expire before the maximum token lifetime, set to '0'.

    Supported units are "s" \(seconds\), "m" \(minutes\), "h" \(hours\), "d" \(days\), "y" \(years\). Units are specified as a single letter following an integer, for example "1d"\(1 day\). If no units are specified, the integer is treated as seconds.

-   **`puppet_enterprise::profile::console::console_ssl_listen_port`**

    Integer representing the port that the console is available on.

    Default: `[443]`

-   **`puppet_enterprise::profile::console::ssl_listen_address`**

    Nginx listen address for the console.

    Default: `"0.0.0.0"`

-   **`puppet_enterprise::profile::console::classifier_prune_threshold`**

    Integer representing the number of days to wait before pruning the size of the classifier database. If you set the value to `"0"`, the node classifier service is never pruned.

-   **`puppet_enterprise::profile::console::classifier_node_check_in_storage`**

    `"true"` to store an explanation of how nodes match each group they're classified into, or `"false"`.

    Default: `"false"`

-   **`puppet_enterprise::profile::console::display_local_time`**

    `"true"` to display timestamps in local time, with hover text showing UTC time, or `"false"` to show timestamps in UTC time.

    Default: `"false"`


Modify these configuration parameters in Hiera or `pe.conf`, not the console:

-   **`puppet_enterprise::api_port`**

    SSL port that the node classifier is served on.

    Default: `[4433]`

-   **`puppet_enterprise::console_services::no_longer_reporting_cutoff`**

    Length of time, in seconds, before a node is considered unresponsive.

    Default: `3600` \(seconds\)


-   **`console_admin_password`**

    The password to log into the console, for example `"myconsolepassword"`.

    Default: Specified during installation.


## Manage the HTTPS redirect

By default, the console redirects to HTTPS when you attempt to connect over HTTP. You can customize the redirect target URL or disable redirection.

### Customize the HTTPS redirect target URL

By default, the redirect target URL is the same as the FQDN of your master, but you can customize this redirect URL.

#### About this task

To change the target URL in the console:

#### Procedure

1.  Click **Classification**, and select the **PE Infrastructure** node group.

2.  On the **Configuration** tab, find the `puppet_enterprise::profile::console::proxy::http_redirect` class, select the `server_name` parameter from the list, and change its value to the desired server.

3.  Click **Add parameter** and commit changes.

4.  On the nodes hosting the master and console, run Puppet.


### Disable the HTTPS redirect

The pe-nginx webserver listens on port 80 by default. If you need to run your own service on port 80, you can disable the HTTPS redirect.

#### Procedure

1.  Add the following to your default `.yaml` file with the parameter set to `false`.

    ```
    puppet_enterprise::profile::console::proxy::http_redirect::enable_http_redirect: false
    ```

2.  To compile changes, run `puppet agent -t` on the master.


## Tuning the PostgreSQL buffer pool size

If you are experiencing performance issues or instability with the console, adjust the buffer memory settings for PostgreSQL.

### About this task

The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.

|**Parameter**|Value|
|**shared\_buffers**|in MB. Set at about 25 percent of your hardware's RAM.|
|**work\_mem**|In MB. Increase the value from the default for large deployments.|

To change the PostgreSQL memory settings using the console:

### Procedure

1.  Click **Classification**, and in the **PE Infrastructure** group, select **PE Database** group.

2.  On the **Configuration** tab, find the **shared\_buffers** and **work\_mem** parameters from the drop down and edit their values as desired.

3.  Click **Add parameter** and commit changes.

4.  On the nodes hosting the master and console, run Puppet.

5.  Restart the PostgreSQL server: `sudo /etc/init.d/pe-postgresql restart`


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

    **Note:** If you enable data editing in the console, add both **Set environment** and **Edit configuration data** to groups that set environment or modify class parameters in order for users to make changes.


### What to do next

If your environment is configured for high availability, you must also update `hiera.yaml` on your replica.

