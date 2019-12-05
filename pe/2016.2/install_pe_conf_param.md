---
layout: default
title: "pe.conf parameter reference"
canonical: "/pe/latest/install_pe_conf_param.html"
---

A `pe.conf` file is a [HOCON](./config_hocon.html) formatted file that declares parameters and values needed to install and configure PE.

[How text-mode installation works](./install_text_mode.html) provides details about running the PE installer in text-mode with `pe.conf`.

The following sections detail the parameters that are mandatory or optional in `pe.conf` when installing or upgrading.

>**Tip:** The example `pe.conf` packaged in the installer directory contains the mandatory parameters needed for a monolithic or split installation. You can use that example file without needing to create your own.

>**Warning:** Do not use single quotes on parameter values. Valid Boolean values are `true` or `false` (case sensitive, no quotation marks). **Do not use Yes (y), No (n), 1, or 0**.

## Required parameters for monolithic installations

The following parameters are mandatory for a [monolithic installation](./install_text_mode_mono.html).

- `"console_admin_password": "<PASSWORD>"`--- The password used to log into the PE console.

- `"puppet_enterprise::puppet_master_host": "<FQDN>"`--- The FQDN of the node hosting the Puppet master. (You can leave this set to the  `"%{::trusted.certname}"` value.)

## Required parameters for split installations

The following parameters are mandatory for a [split installation](./install_text_mode_split.html).

- `"console_admin_password": "<PASSWORD>"`--- The password used to log into the PE console.

   >**Warning**: In a split install, do not use the `"%{::trusted.certname}"` value for the Puppet master. Set the correct FQDN for each component node: `"puppet_enterprise::puppet_master_host":`, `"puppet_enterprise::console_host":` and `puppet_enterprise::puppetdb_host":`.

- `"puppet_enterprise::puppet_master_host": "<FQDN>"`--- The FQDN of the node hosting the Puppet master.

- `"puppet_enterprise::console_host": "<FQDN>"`--- The FQDN of the node hosting the PE console.

- `"puppet_enterprise::puppetdb_host": "<FQDN>"`--- The FQDN of the node hosting PuppetDB.

## Database configuration parameters

The following are default parameters supplied by PE. You can customize them as needed for monolithic or split installations.

> **Note:** If you're upgrading and have used non-default database names and users, you must change the following parameters before upgrading.

- `"puppet_enterprise::activity_database_name": "pe-activity"`--- The name for the activity database.

- `"puppet_enterprise::activity_database_user": "pe-activity"`--- The name for the activity database user.

- `"puppet_enterprise::classifier_database_name": "pe-classifier"`--- The name for the classifier database.

- `"puppet_enterprise::classifier_database_user": "pe-classifier"`--- The name for the classifier database user.

- `"puppet_enterprise::orchestrator_database_name": "pe-orchestrator"`--- The name for the orchestrator database.

- `"puppet_enterprise::orchestrator_database_user": "pe-orchestrator"`--- The name for the orchestrator database user.

- `"puppet_enterprise::puppetdb_database_name": "pe-puppetdb"`--- The name for the PuppetDB database.

- `"puppet_enterprise::puppetdb_database_user": "pe-puppetdb"`--- The name for the PuppetDB database user.

- `"puppet_enterprise::rbac_database_name": "pe-rbac"`--- The name for the RBAC database.

- `"puppet_enterprise::rbac_database_user": "pe-rbac"`--- The password for the RBAC database user.        

### External PostgreSQL parameters

The following parameters are required if you're installing an external postgreSQL. The password parameters can be added to standard installations as needed.

- `"puppet_enterprise::database_host": "<FQDN>"`--- The FQDN of the node hosting the database component.

- `"puppet_enterprise::database_ssl": false`

- `"puppet_enterprise::database_cert_auth": false`

- `"puppet_enterprise::puppetdb_database_password": "<STRING>"`--- The password for the PuppetDB database user.

- `"puppet_enterprise::classifier_database_password": "<STRING>"`--- The password for the classifier database user.

- `"puppet_enterprise::activity_database_password": "<STRING>"`--- The password for the activity database user.

- `"puppet_enterprise::rbac_database_password": "<STRING>"`--- The password for the RBAC database user.

- `"puppet_enterprise::orchestrator_database_password": "<STRING>"`--- The password for the orchestrator database user.

## Additional parameters for monolithic and split installations

You can use these additional parameters as needed.

- `"pe_install::puppet_master_dnsaltnames": ["<STRING>"]` --- DNS altnames to be added to the SSL certificate generated for the Puppet master node. The default `["puppet"]` is used if none are specified.

- ` "puppet_enterprise::profile::master::code_manager_auto_configure": <true or false>`--- Whether or not to automatically configure the code manager service.

- `"puppet_enterprise::profile::master::r10k_remote": "<STRING>"`--- The git URL to be passed to the `r10k.yaml` file (for example, git@<YOUR.GIT.SERVER.COM>:puppet/control.git). This can be any URL that is supported by r10k (and normally git). This answer is only needed if you want r10k configured when PE is installed.

- `"puppet_enterprise::profile::master::r10k_private_key": "<STRING>"`--- The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k (for example, /etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa). This answer is only needed if you want r10k configured when PE is installed. This must be specified in conjunction with `puppet_enterprise::profile::master::r10k_remote`.

- `"puppet_enterprise::use_application_services": <true or false>` --- Whether or not to enable Puppet Application Orchestration. If you choose not to enable this during installation, you can enable it at a later time.

- `"puppet_enterprise::puppetdb_port": "[INTEGER]"` --- The SSL port PuppetDB listens on.

- `"puppet_enterprise::profile::master::check_for_updates": <true or false>` --- Whether to check for updates whenever the pe-puppetserver service restarts. Updates checks are enabled by default. See [Disabling update checking](./config_puppetserver.html#disabling-update-checking) for more information.

- `"puppet_enterprise::master::use_legacy_auth_conf": <true or false>` --- Whether to use a legacy auth.conf file. Refer to [Changes to `auth.conf` when upgrading](./upgrade_mono.html#changes-to-auth.conf-when-upgrading) for details.


## PE settings configured with Hiera or the console

Any PE settings you [configured via Hiera](./config_intro.html#configure-settings-with-hiera) in the default `datadir` (`/etc/puppetlabs/code/environments/%{environment}/hieradata`) will be respected by `pe.conf` when you upgrade.

If you have used **a non-default location** for the Hiera `datadir`, or configured any settings via the PE console, you **must** add these settings to `pe.conf` before upgrading, or they will be overwritten when Puppet runs. [Parameters for configuring and tuning Puppet Enterprise](#Parameters-for-configuring-and-tuning-puppet-enterprise) lists some of these common parameters.

### Parameters for configuring and tuning Puppet Enterprise

You can use these parameters to configure and tune PE as needed. For more details about these settings, refer to [Configuring and tuning your Puppet Enterprise infrastructure](./config_intro.html). 

- `"puppet_enterprise::profile::amq::broker::heap_mb": <INTEGER>` --- The Puppet Master runs an ActiveMQ server to route commands. By default, its process uses a Java heap size of 512 MB. 

- `"puppet_enterprise::profile::master::java_args": {"JSON HASH"}` --- The JVM (Java Virtual Machine) memory that is allocated to the Puppet Server service. 

    For example: `"puppet_enterprise::profile::master::java_args": {"Xmx": "4096m", "Xms": "4096m"}` 

- `"puppet_enterprise::profile::puppetdb::java_args": {"JSON HASH"}` --- The JVM (Java Virtual Machine) memory that is allocated to the PuppetDB service.

    For example: `"puppet_enterprise::profile::master::java_args": {"Xmx": "512m", "Xms": "512m"}`  

- `"puppet_enterprise::profile::console::java_args": {"JSON HASH"}` --- The JVM (Java Virtual Machine) memory that is allocated to console services.

    For example: `"puppet_enterprise::profile::master::java_args": {"Xmx": "512m", "Xms": "512m"}`  

- `"puppet_enterprise::profile::orchestrator::java_args": {"JSON HASH"}` --- The JVM (Java Virtual Machine) memory that is allocated to orchestrations services. 

    For example: `"puppet_enterprise::profile::master::java_args": {"Xmx": "256m", "Xms": "256m"}` 

- `"puppet_enterprise::master::puppetserver::jruby_max_active_instances": <INTEGER>` --- Controls the maximum number of JRuby instances to allow on the Puppet Server.

- `"puppet_enterprise::puppetdb::command_processing_threads": <INTEGER>` --- Defines how many command processing threads PuppetDB uses to sort incoming data.

- `"puppet_enterprise::profile::master::puppetdb::report_processor_ensure": "<present or absent>"` --- Determines if Puppet master generates agent run reports every time Puppet runs and submits these to PuppetDB. This setting is `present` (enabled) by default.

- `"puppet_enterprise::profile::console::classifier_synchronization_period": <INTEGER>`--- Controls how long it takes the node classifier (NC) to retrieve classes from the Puppet master. By default this value is set to 600 seconds (10 minutes). 

- `"puppet_enterprise::master::puppetserver::jruby_max_requests_per_instance": <INTEGER>` --- Sets the maximum number of requests per instance of a JRuby interpreter before it is killed.

- `"puppet_enterprise::master::puppetserver::jruby_environment_class_cache_enabled": <true or false>` --- Specifies whether cached data is used when updating classes in the console. The setting is disabled (`false`) by default.

