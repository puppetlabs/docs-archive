---
layout: default
title: "Complete answer file reference"
canonical: "/pe/latest/install_complete_answer_file_reference.html"
---

The following document provides a comprehensive list of all possible answers that can be used in an answer file.

A .txt file version can be found in the `answers` directory of the PE installer tarball.

See the "answer file overview" and the section on [installing with an answer file](./install_automated.html) for more details.

Installer answers
-----

### Global answers

These answers are always needed.

#### `q_install`

**Y or N** --- Whether to install. Answer files must set this to Y.

#### `q_vendor_packages_install`

**Y or N** --- Whether the installer has permission to install additional packages from the OS's repositories. If set to N, the installation will fail if the installer detects missing dependencies.

#### `q_run_updtvpkg`

**Y or N** --- Only used on AIX. Whether to run the `updtvpkg` command to add info about native libraries to the RPM database. The answer should usually be Y, unless you have special needs around the RPM database.

These answers are only used in some cases, such as when upgrading or reinstalling.

#### `q_backup_and_purge_old_configuration`

**Y or N** --- Whether the installer should backup the existing configuration files and then purge them, or leave them in place. If set to N the installer will fail. This is only needed if Puppet Enterprise was uninstalled without the `-p` option.

#### `q_upgrade_installation`

This option is no longer used, upgrades will be detected automatically.

#### `q_continue_or_reenter_master_hostname`

**C or R** --- Used if the installer cannot reach the Puppet master. If C, the installer will continue; if R, the installer will ask for a new Puppet master hostname. (This answer applies to all roles except for the master role.)

### Roles

These answers are always needed.

#### `q_puppetmaster_install`

**Y or N** --- Whether to install the Puppet master role.

#### `q_puppetdb_install`

**Y or N** --- Whether to install the database support (the PostgreSQL server and PuppetDB) role.

#### `q_puppet_enterpriseconsole_install`

**Y or N** --- Whether to install the console role.

#### `q_puppetagent_install`

**Y or N** --- Whether to install the Puppet agent role.

### Puppet agent answers

These answers are always needed.

#### `q_puppetagent_certname`

**String** --- An identifying string for this agent node. This per-node ID must be unique across your entire site. Fully qualified domain names are often used as agent certnames.

#### `q_puppetagent_server`

**String** --- The hostname of the Puppet master server. For the agent to trust the master's certificate, this must be one of the valid DNS names you chose when installing the Puppet master.

#### `q_fail_on_unsuccessful_master_lookup`

**Y or N** --- Whether to quit the install if the Puppet master cannot be reached.

#### `q_skip_master_verification`

**Y or N** --- This is a silent install option, default is N. When set to Y, the installer will skip master verification which allows the user to deploy agents when they know the master won't be available.

#### `q_puppet_agent_first_run`

**Y or N** --- Controls whether or not the Puppet agent should run after being installed.

### Puppet master answers

These answers are generally needed if you are installing the Puppet master role.

#### `q_all_in_one_install`

**Y or N** --- Whether or not the installation is an all-in-one installation, (i.e., are PuppetDB and the console also being installed on this node).

#### `q_puppetmaster_certname`

**String** --- An identifying string for the Puppet master. This ID must be unique across your entire site. The server's fully qualified domain name is often used as the Puppet master's certname.

#### `q_puppetmaster_dnsaltnames`

**String** --- Valid DNS names at which the Puppet master can be reached. Must be a comma-separated list. In a normal installation, defaults to `<hostname>,<hostname.domain>,puppet,puppet.<domain>`.

#### `q_puppetmaster_enterpriseconsole_hostname`

**String** --- The hostname of the server running the console role. Only needed if you are _not_ installing the console role on the Puppet master server.

#### `q_puppetmaster_enterpriseconsole_port`

**Integer** --- The port on which to contact the console server. Only needed if you are _not_ installing the console role on the Puppet master server.

#### `q_pe_check_for_updates=y`

**y or n; MUST BE LOWERCASE** --- Whether to check for updates whenever the `pe-puppetserver` service restarts. To get the correct update info, the server will pass some basic, anonymous info to Puppet's servers. Specifically, it will transmit:

  * the IP address of the client
  * the type and version of the client's OS
  * the installed version of PE
  * the number of nodes licensed and the number of nodes used

  If you wish to disable update checks (e.g. if your company policy forbids transmitting this information), you will need to set this to `n`. You can also disable checking after installation by editing the `/etc/puppetlabs/installer/answers.install` file.

  If you have a platform that uses systemd (currently RHEL-7 and SLES-12 only), there are additional steps to [disable update checking](./config_puppetserver.html#disabling-update-checking).

#### `q_tarball_server`

**String** --- The location from which PE agent tarballs will be downloaded before installation. Note that agent tarballs are only available for certain operating systems. For details, see [the PE installation instructions](./install_basic.html)

#### `q_exit_and_update_auth_conf=y`

**Y** or **N** --- (Upgrade only) Whether to accept the presence of a modified auth.conf file when upgrading. 

#### `q_puppetmaster_file_sync_service_enabled`

**Y** or **No** --- Whether or not to enable the file sync service.

#### `q_puppetmaster_external_node_terminus`

**Y** or **N** --- Use an external node classifier. **Important**: Use of an external node classifier is **neither supported nor tested**, and you will not be able to access classification-related features in the console, such as Node Management or RBAC.

### Console answers

These answers are generally needed if you are installing the console role.

#### `q_puppet_enterpriseconsole_master_hostname`

**String** --- The hostname of the server running the Puppet master role. Only needed if you are _not_ installing the console role on the Puppet master server.

#### `q_puppet_enterpriseconsole_httpd_port`

**Integer** --- The port on which to serve the console. The default is port 443, which will allow access to the console from a web browser without manually specifying a port. If port 443 is not available, the installer will try port 3000, 3001, 3002, 3003, 3004, and 3005.

#### `q_puppet_enterpriseconsole_auth_password`

**String** --- The password for the console's admin user. Must be longer than eight characters.

#### `q_public_hostname`

**String** ---   A publicly accessible hostname where the console can be accessed if the host name resolves to a private interface (e.g., Amazon EC2). This is set automatically by the installer on EC2 nodes, but can be set manually in environments with multiple hostnames.

#### `q_backup_and_purge_old_database_directory`

**Y or N** --- Whether the installer should backup the existing database directory and then purge it, or leave it in place. If set to N the installer will fail. Used when Puppet Enterprise was uninstalled without the `-d` option.

#### `q_use_application_services`

**Y or N** --- Whether or not to enable Application Orchestration. If you choose not to enable this during installation, you can enable it at a later time.

#### `q_puppetmaster_r10k_remote='git@<your.git.server.com>:puppet/control.git'`

**String** --- The git url to be passed to the `r10k.yaml` file. This can be any URL that is supported by r10k (and normally git). This answer is OPTIONAL and only needed if you would like r10k configured when Puppet Enterprise is installed.

#### `q_puppetmaster_r10k_private_key=/root/.ssh/id_dsa`

**String** --- The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k. This answer is OPTIONAL and only needed if you would like r10k configured when Puppet Enterprise is installed. This must be specified in conjunction with `q_puppetmaster_r10k_remote`.

#### `q_puppetmaster_code_manager_auto_configure`

**Y** or **No** --- (Install only) Whether or not to automatically configure the code manager service.

### Database support answers

These answers are only needed if you are installing the database support role.

#### `q_puppetdb_database_name`

**String** --- The database PuppetDB will use.

#### `q_puppetdb_database_password`

**String** --- The password for PuppetDB's root user.

#### `q_puppetdb_database_user`

**String** --- PuppetDB's root user name.

#### `q_puppetdb_hostname`

**String** --- The hostname of the server running PuppetDB.

#### `q_puppetdb_install`

**Y or N** --- Whether or not to install PuppetDB.

#### `q_puppetdb_port`

**Integer** --- The port where the PuppetDB server can be reached.

#### `q_puppetdb_plaintext_port`

**Integer** --- The port on which PuppetDB accepts plain-text HTTP connections (default port is 8080).

#### `q_classifier_database_name`

**String** --- The database the node classifier will use.

#### `q_classifier_database_password`

**String** --- The password for the node classifier root user.

#### `q_classifier_database_user`

**String** --- The root user name for the node classifier.

#### `q_rbac_database_name`

**String** --- The database the RBAC service will use.

#### `q_rbac_database_password`

**String** --- The password for the RBAC service root user.

#### `q_rbac_database_user`

**String** --- The root user name for the RBAC service.

#### `q_activity_database_name`

**String** --- The database the activity service will use.

#### `q_activity_database_password`

**String** --- The password for the activity service root user.

#### `q_activity_database_user`

**String** --- The root user name for the activity service.

#### `q_orchestrator_database_name`

**String** --- The database the orchestrator service will use.

#### `q_orchestrator_database_password`

**String** --- The password for the orchestration service root user.

#### `q_orchestrator_database_user`

**String** --- The root user name for the Orchestration service.

Uninstaller answers
-----

#### `q_pe_uninstall`

**Y or N** --- Whether to uninstall. Answer files must set this to Y.

#### `q_pe_purge`

**Y or N** --- Whether to purge additional files when uninstalling, including all
  configuration files, modules, manifests, certificates, and the
  home directories of any users created by the PE installer.

#### `q_pe_remove_db`

**Y or N** --- Whether to remove any PE-specific databases when uninstalling.


