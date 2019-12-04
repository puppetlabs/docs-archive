---
layout: default
title: "What gets installed and where?"
canonical: "/pe/latest/install_what_and_where.html"
---

PE installs several software components, configuration files, databases, services and users, and log files. It's useful to know the locations of these should you ever need to troubleshoot or manage your PE infrastructure.   

## Software components installed with PE

PE installs several software components and dependencies.

The functional components of PE are separated between those packaged with the puppet-agent and those packaged on the server side (which also includes the puppet-agent). 

PE 2016.4 includes the following major software components.

{% partial /pe/_versions_2016.md %}

>**Note:** PE also installs other dependencies, as documented in the [system requirements](./sys_req_os.md#dependencies-and-os-specific-details).

## Binaries, modules, and plugins installed with PE

PE installs several binaries, modules, and plugins for normal operations and for interacting with its tools and services.

### Executable binaries and symlinks

PE installs executable binaries for interacting with tools and services. 

On \*nix nodes, all PE software is installed under `/opt/puppetlabs`.

On Windows nodes, all PE software is installed in `Program Files` at `Puppet Labs\Puppet`.

Executable binaries on \*nix are in `/opt/puppetlabs/bin` and `/opt/puppetlabs/sbin`. 

To make essential Puppet tools available to all users, the installer automatically creates symlinks in `/usr/local/bin` for the `facter`, `puppet`, `pe-man`, `r10k`, `hiera`,  and `mco` binaries. Note that the symlinks will only be created if `/usr/local/bin` is writeable.
   
   AIX and Solaris 10/11 users need to add `/usr/local/bin` to their default path.
   
   If you're running Mac OS X agents, note that symlinks are not created until the first successful Puppet run that applies the agents' catalogs.
   
   Binaries provided by other PE components, such as those for interacting with PE's installed PostgreSQL server, PuppetDB, or Ruby packages do not have symlinks created.
   
   For instructions on enabling binaries or disabling symlinks, refer to the following:
   
   - [Add Puppet Enterprise binaries to your `PATH`](./install_pe_mono.html#add-puppet-enterprise-binaries-to-path)
   - [Disable Puppet Enterprise symlinks](./install_pe_mono.html#disable-puppet-enterprise-symlinks)
      
### Modules and plugins

PE installs some modules and plugins for normal operations.

* The Puppet modules included with PE are installed on the Puppet master server in `/opt/puppetlabs/puppet/modules`. Don't modify anything in this directory or add modules of your own. Instead, install them in `/etc/puppetlabs/code/environments/<environment>/modules`.
* MCollective plugins are installed in `/opt/puppetlabs/mcollective/plugins/` on \*nix and in [`<COMMON_APPDATA>`](./install_windows.html#data-directory)`\PuppetLabs\mcollective\etc\plugins\mcollective` on Windows. If you are adding new plugins to your PE agent nodes, you should [distribute them via Puppet as described in the "Adding actions" page of this manual](./orchestration_adding_actions.html).

## Configuration files installed with PE

PE installs configuration files, which, from time to time, you may need to interact with.

On *nix nodes, Puppet Enterprise's configuration files all live under `/etc/puppetlabs/puppet`.

On Windows nodes, Puppet Enterprise's configuration files all live under `<COMMON_APPDATA>\PuppetLabs`. The location of this folder [varies by Windows version](./install_windows.html#data-directory); in 2008 and 2012, its default location is `C:\ProgramData\PuppetLabs\puppet\etc`.

Puppet's `confdir` is in the `puppet` subdirectory. This directory contains the [`puppet.conf`](/guides/configuring.html) file, `auth.conf`, and the SSL directory.

## Tools installed for working with PE

PE installs several suites of tools to help you work with the major components of the software. 

These tools include:

- **Puppet Tools** --- Tools that control basic functions of Puppet such as `puppet master` and `puppet cert.`
    See the [Tools section](/guides/tools.html) of the Puppet Manual for more information.
- **PE client tools** --- The pe-client-tools package collects a set of CLI tools that extend the ability for you to access Puppet Enterprise services from the Puppet master or a workstation. This package includes:   
   - **Puppet orchestrator** --- The Puppet orchestrator is a set of interactive command line tools that provide the interface to the Puppet Application Orchestration service, and also provides you the ability to enforce change on the environment level. These tools include `puppet job` and `puppet app`.  See the [Puppet Orchestrator documentation](./orchestrator_intro.html) for more information. See the [Code Manager documentation](./code_mgr_cli.html) for more information.
   - **Puppet Access** --- Puppet Enterprise users can generate tokens to authenticate their access to certain PE command line tools and API endpoints. See the [token-based authentication documentation](./rbac_token_auth.html) for more information.
   - **Code Manager CLI** --- The `puppet code` command allows you to trigger Code Manager from the command line to deploy your environments.
   - **PuppetDB CLI** --- This a tool for working with PuppetDB, such as building queries and handling exports. See the [PuppetDB CLI]({{puppetdb}}/pdb_client_tools.html) for more information.
- **MCollective Tools** --- Tools used to invoke simultaneous actions across a number of nodes. These tools are built on the MCollective framework and are accessed via the `mco` command. See the [PE MCollective documentation](./orchestration_overview.html) for more information.
- **Module Tools** --- The Module tool is used to access and create Puppet Modules, which are reusable chunks of Puppet code users have written to automate configuration and deployment tasks. For more information, and to access modules, visit the [Puppet Forge](http://forge.puppetlabs.com/).
- **Console** --- The console is Puppet Enterprise's web UI. The console provides tools to view and edit resources on your nodes, view reports and activity graphs, etc. See the [console section](./console_accessing.html) of the PE Manual for more information.

## PE databases installed

PE installs several default databases, all of which use PostgreSQL as a database backend.

The PE PostgreSQL database includes the following databases:

Database      | Description
------------- | ---------------------------------------------------------------
pe-activity   | Activity data from the Classifier, including who, what and when
pe-classifier | Classification data, all Node Group information
pe-puppetdb   | PuppetDB’s data, including exported resources, catalogs, facts, and reports
pe-rbac       | RBAC data, including users, permissions, and AD/LDAP info
pe-orchestrator | orchestrator data, including details about job runs (users, nodes, and run results) 

Use PostgreSQL’s native tools to perform database exports and imports.  At a minimum, you should perform nightly [backups](./maintain_console-db.html#backup-and-restore-procedures-for-the-pe-databases) to a remote system, or as dictated by your company policy.


## Services, users, and group accounts installed

### Services installed

PE installs several services you'll use to interact with it in normal operations.

<table>
 <tr>
   <th>Service</th>
   <th>Definition</th>
 </tr>
 <tr>
   <td nowrap>pe-activemq</td>
   <td>The ActiveMQ message server, which passes messages to the MCollective servers on agent nodes. Runs on servers with the Puppet master component.</td>
 </tr> 
 <tr>
   <td nowrap>pe-console-services</td>
   <td>Manages and serves the PE console.</td>
 </tr>
 <tr>
   <td nowrap>pe-puppetserver</td>
   <td nowrap>The Puppet master server, which manages the Puppet master component.</td>
 </tr>
 <tr>
   <td nowrap>pe-nginx</td>
   <td>Nginx, serves as a reverse-proxy to the PE console.</td>
 </tr>
 <tr>
   <td nowrap>mcollective</td>
   <td>The MCollective daemon, which listens for messages and invokes actions. Runs on every agent node.</td>
 </tr>
 <tr>
   <td nowrap>puppet</td>
   <td>(on EL and Debian-based platforms) --- The Puppet agent daemon. Runs on every agent node.</td>
 </tr>
 <tr>
   <td nowrap>pe-puppetdb, pe-postgresql</td>
   <td>Daemons that manage and serve the database components. Note that pe-postgresql is only created if we install and manage PostgreSQL for you.</td>
 </tr>
 <tr>
   <td nowrap>pxp-agent</td>
   <td>Runs the Puppet agent PXP process.
</td>
 </tr>
 <tr>
   <td nowrap>pe-orchestration-services</td>
   <td>Runs the Puppet orchestration process.</td>
 </tr>
</table> 

### User accounts installed

PE creates several user accounts.

<table>
 <tr>
   <th>User</th>
   <th>Definition</th>
 </tr>
 <tr>
  <td nowrap>peadmin</td>
  <td>An administrative account which can invoke MCollective-related actions. This is the only PE user account intended for use in a login shell. See <a href=https://docs.puppetlabs.com/pe/latest/orchestration_invoke_cli.html>Invoking Actions</a> for more about this user. This user exists on servers with the Puppet master component.</td>
 </tr> 
 <tr>
  <td nowrap>pe-puppet</td>
  <td>A system user that runs the Puppet master processes spawned by pe-puppetserver.</td>
 </tr>
 <tr>
  <td nowrap>pe-webserver</td>
  <td>A system user that runs Nginx (pe-nginx).</td>
 </tr>
 <tr>
  <td nowrap>pe-activemq</td>
  <td>A system user that runs the ActiveMQ message bus used by MCollective.</td>
 </tr>
 <tr>
  <td nowrap>pe-puppetdb</td>
  <td>A system user with root access to the database.</td>
 </tr>
 <tr>
  <td nowrap>pe-postgres</td>
  <td>A system user with access to the pe-postgreSQL instance. Note that this user is only created if we install and manage PostgreSQL for you.</td>
 </tr>
 <tr>
  <td nowrap>pe-console-services</td>
  <td>A system user that runs the console process.</td>
 </tr>
 <tr>
  <td nowrap>pe-orchestration-services</td>
  <td>A system user that runs the Puppet Orchestration process.</td>
 </tr>
</table> 



### Group accounts installed

PE creates several group accounts.

<table>
 <tr>
  <th>Group</th>
  <th>Definition</th>
 </tr>
 <tr>
  <td nowrap>peadmin</td>
  <td>An administrative group which can invoke MCollective-related actions.</td>
 </tr>
 <tr>
  <td nowrap>pe-puppet</td>
  <td>A system group that runs the Puppet master processes spawned by pe-puppetserver.</td>
 </tr>
 <tr>
  <td nowrap>pe-webserver</td>
  <td>A system group that runs Nginx (pe-nginx).</td>
 </tr>
 <tr>
  <td nowrap>pe-activemq</td>
  <td>A system group that runs the ActiveMQ message bus used by MCollective.</td>
 </tr>
 <tr>
  <td nowrap>pe-puppetdb</td>
  <td>A system group with root access to the database.</td>
 </tr>
 <tr>
  <td nowrap>pe-postgres</td>
  <td>A system group with access to the pe-postgreSQL instance. Note that this group is only created if we install and manage PostgreSQL for you.</td>
 </tr>
 <tr>
  <td nowrap>pe-console-services</td>
  <td>A system group that runs the console process.</td>
 </tr>
 <tr>
  <td nowrap>pe-orchestration-services</td>
  <td>A system group that runs the Puppet Orchestration process.</td>
 </tr>
</table>  

## Log files installed with PE

The software distributed with Puppet Enterprise generates log files that you can collect for compliance or use for troubleshooting.

### Puppet master logs

The Puppet master has two logs.

* `/var/log/puppetlabs/puppetserver/puppetserver.log`: the Puppet master application logs its activity here; this is where things like compilation errors and deprecation warnings can be found.
* `/var/log/puppetlabs/puppetserver/puppetserver-daemon.log`: this is where fatal errors or crash reports can be found.

### Puppet agent logs

The locations of Puppet agent logs depend on your agent's operating system.

On *nix nodes, the Puppet agent service logs its activity to the syslog service. Your syslog configuration dictates where these messages will be saved, but the default location is `/var/log/messages` on Linux, `/var/log/system.log` on Mac OS X, and `/var/adm/messages` on Solaris.

On Windows nodes, the Puppet agent service logs its activity to the Windows Event Log. You can view its logs by browsing the Event Viewer. (**Control Panel** > **System and Security** > **Administrative Tools** > **Event Viewer**)

### ActiveMQ logs

ActiveMQ has three logs.

- `/var/log/puppetlabs/activemq/wrapper.log`
- `/var/log/puppetlabs/activemq/activemq.log`
- `/var/log/puppetlabs/activemq/data/audit.log`

### MCollective logs

MCollective has three logs.

- `/var/log/puppetlabs/mcollective.log`: maintained by the MCollective service, which is installed on all nodes.
- `/var/log/puppetlabs/mcollective-audit.log`: exists on all nodes that have MCollective installed; logs any MCollective actions run on the node, including information about the client that called the node

### Console and console-services logs

The console and console-services has four logs. 

- `/var/log/puppetlabs/nginx/error.log`: contains errors related to nginx. Console errors that don't get logged anywhere else can be found in this log. If you have problems with the console or Puppet, this log may be useful.
- `/var/log/puppetlabs/console-services.log`
- `/var/log/puppetlabs/console-services-access.log`
- `/var/log/puppetlabs/console-services-daemon.log`: this is where fatal errors or crash reports can be found.

### Installer logs

The PE installer has three logs.

- `/var/log/puppetlabs/installer/http.log`: contains the web requests sent to the installer; present only on the machine from which the web-based install was performed
- `/var/log/puppetlabs/installer/installer-<timestamp>.log`: contains the operations performed and any errors that occurred during installation
- `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log`: contains the contents of the last installer run

### PE Database logs

PE has two logs for its databases.

- `/var/log/puppetlabs/puppetdb/puppetdb.log`
- `/var/log/puppetlabs/postgresql/pgstartup.log`

### Orchestration-services logs

There are two log locations for orchestration-services.

- `/var/log/puppetlabs/orchestration-services.log`
- `/var/log/puppetlabs/orchestration-services-daemon.log`: this is where fatal errors or crash reports can be found.

## Certificates installed with PE

During installation, PE generates and installs a number of SSL certificates so agents and services can authenticate themselves.

These certs can be found at `/etc/puppetlabs/puppet/ssl/certs`.

<table>
 <tr>
  <th>Cert</th>
  <th>Definition</th>
 </tr>
 <tr>
  <td nowrap>&lt;PUPPET MASTER CERTNAME&gt;</td>
  <td>Generated during install. In a monolithic install, this cert is used by PuppetDB and the PE console. This is the same value for the agent's certname that runs on the Puppet master. In monolithic install, the agent on the PE console and PuppetDB share this certname. In a default monolithic or split install, this is also the Puppet CA cert.</td>
 </tr>
 <tr>
  <td nowrap>&lt;PE CONSOLE CERTNAME&gt;</td>
  <td>The certificate for the PE console, which is only generated if you have a split install. This is the same value for the agent's certname that runs on the PE console.</td>
 </tr>
 <tr>
  <td nowrap>&lt;PUPPETDB CERTNAME&gt;</td>
  <td>The certificate for PuppetDB, which is only generated if you have a split install. This is the same value for the agent's certname that runs on PuppetDB.</td>
 </tr>
 <tr>
  <td nowrap>pe-internal-mcollective-servers</td>
  <td>A certificate generated on the Puppet master and shared to all agent nodes.</td>
 </tr>
 <tr>
  <td nowrap>pe-internal-peadmin-mcollective-client</td>
  <td>The certificate for the peadmin account on the Puppet master.</td>
 </tr>
 <tr>
  <td nowrap>pe-internal-puppet-console-mcollective-client</td>
  <td>The MCollective certificate for the PE console.</td>
 </tr>
</table>   

Services that run on the Puppet master or console (for example, `pe-orchestration-services` and `pe-console-services`, use the Puppet agent certificate to authenticate. 

************
**Related links**

- [Installing the PE license key](./install_license_key.html)