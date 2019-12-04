---
layout: default
title: "PE configuration settings in pe.conf"
canonical: "/pe/latest/install_pe_conf_param.html"
---

## pe.conf parameter reference

The following sections detail the parameters that are mandatory or optional in `pe.conf` when installing or upgrading.

A `pe.conf` file is a [HOCON](./config_hocon.html) formatted file that declares parameters and values needed to install and configure PE.

The following are examples of valid paramater and value expressions:

Type | Value
-----|------------
FQDNs | `"puppet_enterprise::puppet_master_host": "master.example.com"`
Strings | `"console_admin_password": "mypassword"`
Booleans | `"puppet_enterprise::profile::master::app_management": true` <br> Valid Boolean values are `true` or `false` (case sensitive, no quotation marks). **Do not use Yes (y), No (n), 1, or 0**.
JSON hashes | `"puppet_enterprise::profile::orchestrator::java_args": {"Xmx": "256m", "Xms": "256m"}`
Integer | `"puppet_enterprise::profile::console::rbac_session_timeout": "60"`

>**Warning:** Do not use single quotes on parameter values. Use double quotes as shown in the examples.

The example `pe.conf` packaged in the installer directory contains the mandatory parameters needed for a monolithic or split installation. You can use that example file without needing to create your own.

### Parameters for monolithic installations

The following parameters are mandatory for a [monolithic installation](./install_text_mode_mono.html).

<table>
  <tr>
    <th>Parameter</th>
    <th> Value </th>
  </tr>
  <tr>
    <td><code>console_admin_password</code></td>
    <td nowrap>The password used to log into the PE console. For example, <code>"myconsolepassword"</code></td>
  </tr>
  <tr>
    <td><code>puppet_enterprise::puppet_master_host</code></td>
    <td nowrap>The FQDN of the node hosting the Puppet master. For example, <code>"PUPPET MASTER NODE FQDN"</code><br> (In a monolithic installation, you can leave this set to the <code>"%{::trusted.certname}"</code> value.)</td>
  </tr>
 </table>

### Parameters for split installations

The following parameters are mandatory for a [split installation](./install_text_mode_split.html).

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><code>console_admin_password</code></td>
    <td nowrap>The password used to log into the PE console. For example, <code>"myconsolepassword"</code></td>
  </tr>
  <tr>
    <td><code>puppet_enterprise::puppet_master_host</code></td>
    <td nowrap>The FQDN of the node hosting the Puppet master. For example, <code>"PUPPET MASTER NODE FQDN"</code><br> (In a split install,<br> do not use the <code>"%{::trusted.certname}"</code> value for the Puppet master.)</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::console_host</code></td>
    <td nowrap>The FQDN of the node hosting the PE console. For example, <code>"PE CONSOLE NODE FQDN"</code></td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::puppetdb_host</code></td>
    <td nowrap><code>The FQDN of the node hosting PuppetDB. For example, "PUPPETDB NODE FQDN"</code></td>
  </tr>
 </table>

### Database configuration parameters

The following are default parameters and values supplied for the PE databases. These should not be changed or customized.

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::activity_database_name</code></td>
    <td nowrap><code>pe-activity</code> (Default)<br>The name for the activity database.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::activity_database_user</code></td>
    <td nowrap><code>pe-activity</code>(Default)<br>The name for the activity database user.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::classifier_database_name</code></td>
    <td nowrap><code>pe-classifier</code>(Default)<br>The name for the classifier database.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::classifier_database_user</code></td>
    <td nowrap><code>pe-classifier</code>(Default)<br>The name for the classifier database user.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::orchestrator_database_name</code></td>
    <td nowrap><code>pe-orchestrator</code>(Default)<br>The name for the orchestrator database.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::orchestrator_database_user</code></td>
    <td nowrap><code>pe-orchestrator</code>(Default)<br>The name for the orchestrator database user.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::puppetdb_database_name</code></td>
    <td nowrap><code>pe-puppetdb</code>(Default)<br>The name for the PuppetDB database.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::puppetdb_database_user</code></td>
    <td nowrap><code>pe-puppetdb</code>(Default)<br>The name for the PuppetDB database user.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::rbac_database_name</code></td>
    <td nowrap><code>pe-rbac</code>(Default)<br>The name for the RBAC database.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::rbac_database_user</code></td>
    <td nowrap><code>pe-rbac</code>(Default)<br>The password for the RBAC database user.</td>
   </tr>
</table>

### External PostgreSQL parameters

The following parameters are required if you're installing an external postgreSQL. The password parameters can be added to standard installations if needed.

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::database_host</code></td>
    <td nowrap>The FQDN of the node hosting the database component.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::database_ssl</code></td>
    <td><code>"true"</code> or <code>"false"</code> (Default is <code>"true"</code>.) <br>Leave this value as is for external PostgreSQL.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::database_cert_auth</code></td>
    <td><code>"true"</code> or <code>"false"</code>(Default is <code>"true"</code>.)<br>Leave this value as is for external PostgreSQL. </td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::puppetdb_database_password</code></td>
    <td nowrap>The password for the PuppetDB database user. Must be a string, for example, <code>"mypassword"</code></td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::classifier_database_password</code></td>
    <td nowrap>The password for the classifier database user. Must be a string, for example, <code>"mypassword"</code></td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::activity_database_password</code></td>
    <td nowrap>The password for the activity database user. Must be a string, for example, <code>"mypassword"</code> </td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::rbac_database_password</code></td>
    <td nowrap>The password for the RBAC database user. Must be a string, for example, <code>"mypassword"</code></td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::orchestrator_database_password</code></td>
    <td nowrap>The password for the orchestrator database user. Must be a string, for example, <code>"mypassword"</code></td>
   </tr>
</table>

### Parameters for configuring and tuning Puppet Enterprise

You can use these parameters to [configure and tune PE](./config_intro.html) as needed.

**Parameters for configuring and tuning the Puppet master**

<table>
  <tr>
   <th>Parameter</th>
   <th>Value</th>
   </tr>
   <tr>
    <td no wrap><code>pe_install::puppet_master_dnsaltnames</code></td>
    <td nowrap>Must be a string. DNS altnames to be added to the SSL certificate generated for the Puppet master node. <br>The default <code>["puppet"]</code> is used if none are specified. </td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::certificate_authority</code></td>
     <td nowrap>Include an array of additional certificates to be allowed access to the <code>/certificate_status</code><br> API endpoint. This list is additive to the base PE certificate list. For example, <code>"examplevm.puppet.com"</code></td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::master::code_manager_auto_configure</code></td>
     <td nowrap>Set to <code>true</code> or <code>false</code><br>Whether or not to automatically configure the code manager service.</td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::master::r10k_remote</code></td>
     <td nowrap>Must be a string. <br>The git URL to be passed to the r10k.yaml file. <br>For example, <code>"git@your.git.server.com:puppet/control.git"</code>. <br> This can be any URL that is supported by r10k (and normally git). This answer is only needed<br> if you want r10k configured when PE is installed.</td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::master::r10k_private_key</code></td>
     <td nowrap>Must be a string. <br>The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k.<br> For example, <code>"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"</code>).<br> This answer is only needed if you want r10k configured when PE is installed. This must be specified in conjunction with <br><code>puppet_enterprise::profile::master::r10k_remote</code>.</td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::master::check_for_updates</code></td>
     <td nowrap>Set to <code>true</code> or <code>false</code> (Default is <code>true</code>.) <br>Whether to check for updates whenever<br> the pe-puppetserver service restarts. Update checks are enabled (<code>true</code>) by default. <br>See <a href="https://docs.puppet.com/pe/latest/config_puppetserver.html#disabling-update-checking">Disabling update checking</a>for more information.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::profile::mcollective::peadmin::mco_loglevel</code></td>
    <td>A string that represents the log level of the MCollective client log. Change from <code>info </code>to <code>debug</code>, <code>warning</code>, or <code>error</code> as needed.</td>
   </tr>
</table>

**Parameters for configuring and tuning the PE console and console services**

<table>
 <tr>
   <th>Parameter</th>
   <th>Value</th>
 </tr>
 <tr>
   <td nowrap><code>puppet_enterprise::profile::console::classifier_synchronization_period</code></td>
   <td nowrap>Must be an integer. Classifier synchronization period.<br> Controls how long it takes the node classifier (NC) to retrieve classes <br> from the Puppet master. Default is <code>"600"</code> seconds (10 minutes).</td>
 </tr>
 <tr>
   <td nowrap><code>puppet_enterprise::api_port</code></td>
   <td>The SSL port PE serves the node classifier on. Defaults to <code>"[4433]"</code>.</td>
  </tr>
  <tr>
   <td nowrap><code>puppet_enterprise::profile::console::rbac_session_timeout</code></td>
   <td>Must be an integer. Specifies how long a user's session should last, in minutes. This session is the same across node classification, RBAC, and the console. The default value is <code>"60"</code>.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::console::session_maximum_lifetime</code></td>
    <td>Must be an integer. Specifies the maximum allowable period that a console session can be valid for. Supported units are "s" (seconds), "m" (minutes), "h" (hours), "d" (days), "y" (years). May be set to "0" to not expire before the maximum token lifetime. <br>Units are specified as a single letter following an integer, for example <code>"1d"</code> (1 day). If no units are specified, the integer is treated as seconds.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::console::console_ssl_listen_port</code></td>
    <td>Must be an integer. The port the console is available on.<br> Default is <code>"[443]"</code>.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::console::ssl_listen_address</code></td>
    <td>PE's nginx listen address for the PE console. <br>Defaults to <code>"0.0.0.0"</code>.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::profile::console::classifier_prune_threshold</code></td>
    <td>Must be an integer. The number of days to wait before pruning the size of the classifier database. <br>If you set the value to <code>"0"</code>, the node classifier service will never prune the database.</td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::console::pcp_timeout</code></td>
     <td>A Puppet agent needs to connect to the PCP broker in order to do Puppet runs via the <b>Run Puppet</b> button in the console. Set an integer to specify how much time should pass before the connection times out. <br>The <b>Run Puppet</b> button defaults to <code>"5"</code> seconds. If the agent can’t connect to the broker in that time frame, the run will timeout.</td>
   </tr>
</table>

**Parameters for configuring and tuning the orchestrator and orchestration services**

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::master::app_management</code></td>
    <td nowrap>Disable or enable application management. Set to <code>true</code> or <code>false</code>.<br>Enabled (<code>true</code>) by default.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::orchestrator::run_service</code></td>
    <td>Disable or enable orchestration services. Set to <code>true</code> or <code>false</code>.<br>Enabled (<code>true</code>) by default.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::agent::pxp_enabled</code></td>
    <td>Disable or enable the PXP service. Set to <code>true</code> or <code>false</code>.<br>If you disable this setting you can’t use the orchestrator or the <b>Run Puppet</b> button in the console. <br>Enabled (<code>true</code>) by default.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::orchestrator::global_concurrent_compiles</code></td>
    <td>An integer that determines how many concurrent compile requests can be outstanding to the Puppet master, across all orchestrator jobs. <br>The default value is <code>"8"</code>.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::orchestrator::pcp_timeout</code></td>
    <td>A Puppet agent needs to connect to the PCP broker in order to do Puppet runs via the Puppet orchestrator. Set an integer to specify how much time should pass before the connection times out.<br> The Puppet orchestrator defaults to <code>"30"</code> seconds. If the agent can’t connect to the broker in that time frame, the run will timeout.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::console::display_local_time</code></td>
    <td>By default, the console displays timestamps in UTC format (also known as Zulu time). If you prefer, you can change your console settings to display all timestamps in local time, with UTC time shown on hover. Set to <code>true</code> to display timestamps in local time, with hover text showing UTC time or <code>false</code> (default) to show timestamps in UTC time.</td>
   </tr>
   <tr>
    <td nowrap><code>puppet_enterprise::profile::console::classifier_node_check_in_storage</code></td>
    <td>The node classifier service can store a check-in for each node when its classification is requested that includes an explanation of how it matched the rule of every group it was classified into. This is disabled by default. Default is <code>false</code>. Set to <code>true</code>to enable.</td>
  </tr>
</table>

**Parameters for configuring and tuning PuppetDB**

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::puppetdb::command_processing_threads</code></td>
    <td nowrap>Must be an integer. Define how many command processing threads<br> PuppetDB uses to sort incoming data. Each thread can process a single command at a time.<br> This setting defaults to half the number of cores in your system. For example, <code>"8"</code>.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::master::puppetdb_report_processor_ensure</code></td>
    <td>The Puppet master generates agent run reports every time Puppet runs and submits<br> these to PuppetDB. <br>Set to <code>present</code> or <code>absent</code>. Default is set to <code>present</code>(enabled).</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::puppetdb_port</code></td>
    <td>The SSL port PuppetDB listens on. Must be an integer in brackets. <br>For example, <code>"[8081]"</code>.</td>
  </tr>
  <tr>
    <td nowrap><code>puppet_enterprise::profile::puppetdb::node_purge_ttl</code></td>
    <td>Sets the “time-to-live” value before PE automatically deletes nodes that have been <br> deactivated or expired. This will also delete all facts, catalogs, and reports for the relevant nodes. <br>For example, a value of <code>"14d"</code> would set the time-to-live to 14 days.</td>
  </tr>
</table>

**Parameters for configuring and tuning Java arguments**

<table>
 <tr>
   <th>Parameter</th>
   <th>Value</th>
 </tr>
 <tr>
     <td nowrap><code>puppet_enterprise::profile::master::java_args</code></td>
     <td nowrap>The JVM (Java Virtual Machine) memory that is allocated to the Puppet Server service.<br> Set as a JSON hash. For example, <code>{"Xmx": "4096m", "Xms": "4096m"}</code></td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::puppetdb::java_args</code></td>
     <td nowrap>The JVM memory that is allocated to the PuppetDB service.<br> Set as a JSON hash. For example, <code>{"Xmx": "512m", "Xms": "512m"}</code></td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::console::java_args</code></td>
     <td nowrap>The JVM memory that is allocated to console services.<br> Set as a JSON hash. For example, <code>{"Xmx": "512m", "Xms": "512m"}</code></td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::orchestrator::java_args</code> </td>
     <td nowrap>The JVM memory that is allocated to orchestrations services. <br> Set as a JSON hash. For example, <code>{"Xmx": "256m", "Xms": "256m"}</code></td>
   </tr>
</table>