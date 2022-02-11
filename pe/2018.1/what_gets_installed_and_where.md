# What gets installed and where?

Puppet Enterprise installs several software components, configuration files, databases, services and users, and log files. It's useful to know the locations of these should you ever need to troubleshoot or manage your infrastructure.

## Software components installed

PE installs several software components and dependencies. These tables show which version of each component is installed for releases dating back to the previous long term supported \(LTS\) release.

The functional components of the software are separated between those packaged with the agent and those packaged on the server side \(which also includes the agent\).

**Note:** PE also installs other dependencies, as documented in the system requirements.

This table shows the components installed on all agent nodes.

**Note:** Hiera 5 is a backwards-compatible evolution of Hiera, which is built into Puppet 4.9.0 and higher. To provide some backwards-compatible features, it uses the classic Hiera 3.x.x codebase version listed in this table.

|PE Version|Puppet Agent|Puppet|Facter|Hiera|MCollective|Ruby|OpenSSL|
|----------|------------|------|------|-----|-----------|----|-------|
|2018.1.18|5.5.22|5.5.22|3.11.14|3.4.6|2.12.5|2.4.10|1.1.1g|
|2018.1.17|5.5.22|5.5.22|3.11.14|3.4.6|2.12.5|2.4.10|1.1.1g|
|2018.1.16|5.5.21|5.5.21|3.11.14|3.4.6|2.12.5|2.4.10|1.1.1g|
|2018.1.15|5.5.20|5.5.20|3.11.13|3.4.6|2.12.5|2.4.10|1.0.2u|
|2018.1.13|5.5.19|5.5.19|3.11.12|3.4.6|2.12.5|2.4.9|1.0.2u|
|2018.1.12|5.5.18|5.5.18|3.11.11|3.4.6|2.12.5|2.4.9|1.0.2t|
|2018.1.11|5.5.17|5.5.17|3.11.10|3.4.6|2.12.5|2.4.9|1.0.2t|
|2018.1.9|5.5.16|5.5.16|3.11.9|3.4.6|2.12.4|2.4.5|1.0.2r|
|2018.1.8|5.5.14|5.5.14|3.11.8|3.4.6|2.12.4|2.4.5|1.0.2r|
|2018.1.7|5.5.10|5.5.10|3.11.7|3.4.6|2.12.4|2.4.5|1.0.2n|
|2018.1.5|5.5.8|5.5.8|3.11.6|3.4.5|2.12.4|2.4.4|1.0.2n|
|2018.1.4|5.5.6|5.5.6|3.11.4|3.4.4|2.12.3|2.4.4|1.0.2n|
|2018.1.3|5.5.4|5.5.3|3.11.3|3.4.3|2.12.2|2.4.4|1.0.2n|
|2018.1.2|5.5.3|5.5.2|3.11.2|3.4.3|2.12.2|2.4.4|1.0.2n|
|2018.1.0|5.5.1|5.5.1|3.11.1|3.4.3|2.12.1|2.4.4|1.0.2n|
|2017.3.10|5.3.8|5.3.7|3.9.6|3.4.3|2.11.5|2.4.4|1.0.2n|
|2017.3.9|5.3.8|5.3.7|3.9.6|3.4.3|2.11.5|2.4.4|1.0.2n|
|2017.3.9|5.3.8|5.3.7|3.9.6|3.4.3|2.11.5|2.4.4|1.0.2n|
|2017.3.8|5.3.8|5.3.7|3.9.6|3.4.3|2.11.5|2.4.4|1.0.2n|
|2017.3.6|5.3.6|5.3.6|3.9.6|3.4.3|2.11.5|2.4.4|1.0.2n|
|2017.3.5|5.3.5|5.3.5|3.9.5|3.4.2|2.11.4|2.4.3|1.0.2n|
|2017.3.4|5.3.4|5.3.4|3.9.4|3.4.2|2.11.4|2.4.3|1.0.2n|
|2017.3.2|5.3.3|5.3.3|3.9.3|3.4.2|2.11.4|2.4.2|1.0.2k|
|2017.3.1|5.3.2|5.3.2|3.9.2|3.4.2|2.11.3|2.4.1|1.0.2k|
|2017.3.0|5.3.2|5.3.2|3.9.2|3.4.2|2.11.3|2.4.1|1.0.2k|
|2017.2.5|1.10.9|4.10.9|3.6.8|3.3.2|2.10.6|2.1.9|1.0.2k|
|2017.2.4|1.10.8|4.10.8|3.6.7|3.3.2|2.10.5|2.1.9|1.0.2k|
|2017.2.3|1.10.5|4.10.5|3.6.6|3.3.2|2.10.5|2.1.9|1.0.2k|
|2017.2.2|1.10.4|4.10.4|3.6.5|3.3.2|2.10.5|2.1.9|1.0.2k|
|2017.2.1|1.10.1|4.10.1|3.6.4|3.3.1|2.10.4|2.1.9|1.0.2k|
|2017.1.1|1.9.3|4.9.4|3.6.2|3.3.1|2.9.0|2.1.9|1.0.2j|
|2017.1.1 for Ubuntu on i386|1.7.0|4.7.0|3.4.1|3.2.1|2.10.2|2.1.9|1.0.2h|
|2017.1.0|1.9.3|4.9.4|3.6.2|3.3.1|2.9.0|2.1.9|1.0.2j|
|2017.1.0 for Ubuntu on i386|1.7.0|4.7.0|3.4.1|3.2.1|2.10.2|2.1.9|1.0.2h|
|2016.5.2|1.8.3|4.8.2|3.5.1|3.2.2|2.9.1|2.1.9|1.0.2j|
|2016.5.2 for Ubuntu on i386|1.7.0|4.7.0|3.4.1|3.2.1|2.9.0|2.1.9|1.0.2h|
|2016.5.1|1.8.2|4.8.1|3.5.0|3.2.2|2.9.1|2.1.9|1.0.2j|
|2016.5.1 for Ubuntu on i386|1.7.0|4.7.0|3.4.1|3.2.1|2.9.0|2.1.9|1.0.2h|
|2016.4.15|1.10.14|4.10.12|3.6.10|3.3.3|2.10.6|2.1.9|1.0.2n|
|2016.4.14|1.10.14|4.10.12|3.6.10|3.3.3|2.10.6|2.1.9|1.0.2n|
|2016.4.13|1.10.14|4.10.12|3.6.10|3.3.3|2.10.6|2.1.9|1.0.2n|
|2016.4.11|1.10.12|4.10.11|3.6.10|3.3.3|2.10.6|2.1.9|1.0.2n|
|2016.4.10|1.10.10|4.10.10|3.6.9|N/A|2.10.6|2.1.9|1.0.2n|
|2016.4.9|1.10.9|4.10.9|3.6.8|N/A|2.10.6|2.1.9|1.0.2k|
|2016.4.8|1.10.8|4.10.8|3.6.7|N/A|2.10.5|2.1.9|1.0.2k|
|2016.4.7|1.10.5|4.10.5|3.6.6|N/A|2.10.5|2.1.9|1.0.2k|
|2016.4.6|1.10.4|4.10.4|3.6.5|N/A|2.10.5|2.1.9|1.0.2k|
|2016.4.5|1.10.1|4.10.1|3.6.4|N/A|2.10.4|2.1.9|1.0.2k|
|2016.4.3|1.7.2|4.7.1|3.4.2|3.2.2|2.9.1|2.1.9|1.0.2j|
|2016.4.3 for Ubuntu on i386|1.7.1|4.7.0|3.4.1|3.2.1|2.9.0|2.1.9|1.0.2j|
|2016.4.2|1.7.1|4.7.0|3.4.1|3.2.1|2.9.0|2.1.9|1.0.2j|
|2016.4.0|1.7.1|4.7.0|3.4.1|3.2.1|2.9.0|2.1.9|1.0.2j|

This table shows components installed on server nodes:

|PE Version|Puppet Server|PuppetDB|r10k|Razor Server|Razor Libs|PostgreSQL|Java|ActiveMQ|Nginx|
|----------|-------------|--------|----|------------|----------|----------|----|--------|-----|
|2018.1.18|5.3.16|5.2.19|2.6.9|1.9.9|N/A|9.6.20|1.8.0|5.15.5|1.17.10|
|2018.1.17|5.3.16|5.2.19|2.6.9|1.9.9|N/A|9.6.19|1.8.0|5.15.5|1.17.10|
|2018.1.16|5.3.14|5.2.18|2.6.8|1.9.9|N/A|9.6.18|1.8.0|5.15.5|1.17.10|
|2018.1.15|5.3.13|5.2.15|2.6.8|1.9.6|N/A|9.6.17|1.8.0|5.15.5|1.16.1|
|2018.1.13|5.3.12|5.2.13|2.6.8|1.9.6|N/A|9.6.17|1.8.0|5.15.5|1.16.1|
|2018.1.12|5.3.11|5.2.12|2.6.7|1.9.6|N/A|9.6.16|1.8.0|5.15.5|1.16.1|
|2018.1.11|5.3.10|5.2.11|2.6.7|1.9.2|N/A|9.6.15|1.8.0|5.15.5|1.16.1|
|2018.1.9|5.3.9|5.2.9|2.6.6|1.9.2|N/A|9.6.13|1.8.0|5.15.5|1.14.2|
|2018.1.8|5.3.8|5.2.8|2.6.5|1.9.2|N/A|9.6.12|1.8.0|5.15.5|1.14.2|
|2018.1.7|5.3.7|5.2.7|2.6.5|1.9.2|N/A|9.6.10|1.8.0|5.15.5|1.14.0|
|2018.1.5|5.3.6|5.2.6|2.6.5|1.9.2|N/A|9.6.10|1.8.0|5.15.5|1.14.0|
|2018.1.4|5.3.5|5.2.4|2.6.2|1.9.2|N/A|9.6.10|1.8.0|5.15.3|1.14.0|
|2018.1.3|5.3.4|5.2.4|2.6.2|1.9.2|N/A|9.6.8|1.8.0|5.15.3|1.14.0|
|2018.1.2|5.3.3|5.2.2|2.6.2|1.9.2|N/A|9.6.8|1.8.0|5.15.3|1.12.1|
|2018.1.0|5.3.2|5.2.2|2.6.2|1.8.1|N/A|9.6.8|1.8.0|5.15.3|1.12.1|
|2017.3.10|5.1.6|5.1.5|2.6.0|1.6.0|3.1.2|9.6.10|1.8.0|5.15.3|1.14.0|
|2017.3.9|5.1.6|5.1.5|2.6.0|1.6.0|3.1.2|9.6.8|1.8.0|5.15.3|1.14.0|
|2017.3.8|5.1.6|5.1.5|2.6.0|1.6.0|3.1.2|9.6.8|1.8.0|5.15.3|1.12.1|
|2017.3.6|5.1.6|5.1.5|2.6.0|1.6.0|3.1.2|9.6.8|1.8.0|5.15.3|1.12.1|
|2017.3.5|5.1.6|5.1.4|2.6.0|1.6.0|3.1.2|9.6.6|1.8.0|5.14.3|1.12.1|
|2017.3.4|5.1.5|5.1.4|2.6.0|1.6.0|3.1.2|9.6.6|1.8.0|5.14.3|1.12.1|
|2017.3.2|5.1.4|5.1.3|2.5.5|1.6.0|3.1.2|9.6.5|1.8.0|5.14.3|1.12.1|
|2017.3.1|5.1.3|5.1.1|2.5.5|1.6.0|3.1.2|9.6.5|1.8.0|5.14.3|1.12.1|
|2017.3.0|5.1.3|5.1.1|2.5.5|1.6.0|3.1.2|9.6.5|1.8.0|5.14.3|1.12.1|
|2017.2.5|2.8.0|4.4.2|2.5.5|1.6.0|3.1.2|9.4.14|1.8.0|5.14.3|1.12.1|
|2017.2.4|2.8.0|4.4.2|2.5.5|1.6.0|3.1.2|9.4.14|1.8.0|5.14.3|1.12.1|
|2017.2.3|2.7.2|4.4.1|2.5.5|1.6.0|3.1.2|9.4.12|1.8.0|5.14.3|1.12.1|
|2017.2.2|2.7.2|4.4.1|2.5.5|1.6.0|3.1.2|9.4.12|1.8.0|5.14.3|1.10.2|
|2017.2.1|2.7.2|4.4.0|2.5.4|1.6.0|3.1.2|9.4.10|1.8.0|5.14.3|1.10.2|
|2017.1.1|2.7.2|4.3.2|2.5.1|1.5.0|3.1.2|9.4.10|1.8.0|5.14.3|1.10.2|
|2017.1.0|2.7.2|4.3.2|2.5.1|1.5.0|3.1.2|9.4.10|1.8.0|5.14.3|1.10.2|
|2016.5.2|2.6.0|4.2.5|2.5.0|1.5.0|3.1.2|9.4.9|1.8.0|5.14.3|1.8.1|
|2016.5.1|2.6.0|4.2.5|2.5.0|1.5.0|3.1.2|9.4.9|1.8.0|5.13.2|1.8.1|
|2016.4.15|2.6.1|4.2.3|2.5.5|1.4.0|3.1.2|9.4.19|1.8.0|5.15.3|1.14.0|
|2016.4.14|2.6.1|4.2.3|2.5.5|1.4.0|3.1.2|9.4.17|1.8.0|5.15.3|1.14.0|
|2016.4.13|2.6.1|4.2.3|2.5.5|1.4.0|3.1.2|9.4.17|1.8.0|5.15.3|1.12.1|
|2016.4.11|2.6.1|4.2.3|2.5.5|1.4.0|3.1.2|9.4.17|1.8.0|5.15.3|1.12.1|
|2016.4.10|2.6.1|4.2.3|2.5.5|1.4.0|3.1.2|9.4.15|1.8.0|5.14.3|1.12.1|
|2016.4.9|2.6.0|4.2.3|2.5.5|1.4.0|3.1.2|9.4.14|1.8.0|5.14.3|1.12.1|
|2016.4.8|2.6.0|4.2.3|2.5.5|1.4.0|3.1.2|9.4.14|1.8.0|5.14.3|1.12.1|
|2016.4.7|2.6.0|4.2.3|2.5.5|1.4.0|3.1.2|9.4.12|1.8.0|5.14.3|1.12.1|
|2016.4.6|2.6.0|4.2.3|2.5.5|1.4.0|3.1.2|9.4.12|1.8.0|5.14.3|1.8.1|
|2016.4.5|2.6.0|4.2.3|2.5.4|1.4.0|3.1.2|9.4.9|1.8.0|5.14.3|1.8.1|
|2016.4.3|2.6.0|4.2.3|2.4.5|1.4.0|3.1.2|9.4.9|1.8.0|5.14.3|1.8.1|
|2016.4.2|2.6.0|4.2.3|2.4.3|1.4.0|3.1.2|9.4.9|1.8.0|5.13.2|1.8.1|
|2016.4.0|2.6.0|4.2.3|2.4.3|1.4.0|3.1.2|9.4.9|1.8.0|5.13.2|1.8.1|

## Executable binaries and symlinks installed

PE installs executable binaries and symlinks for interacting with tools and services.

On \*nix nodes, all software is installed under `/opt/puppetlabs`.

On Windows nodes, all software is installed in `Program Files` at `Puppet Labs\Puppet`.

Executable binaries on \*nix are in `/opt/puppetlabs/bin` and `/opt/puppetlabs/sbin`.

**Tip:** To include binaries in your default `$PATH`, manually add them to your profile or export the path:

```
export PATH=$PATH:/opt/puppetlabs/bin
```

To make essential Puppet tools available to all users, the installer automatically creates symlinks in `/usr/local/bin` for the `facter`, `puppet`, `pe-man`, `r10k`, `hiera`, and `mco` binaries. Symlinks are created only if `/usr/local/bin` is writeable. Users of AIX and Solaris versions 10 and 11 must add `/usr/local/bin` to their default path.

For macOS agents, symlinks aren't created until the first successful run that applies the agents' catalogs.

**Tip:** You can disable symlinks by changing the `manage_symlinks` setting in your default Hiera file:

```
puppet_enterprise::manage_symlinks: false
```

Binaries provided by other software components, such as those for interacting with the PostgreSQL server, PuppetDB, or Ruby packages, do not have symlinks created.

## Modules and plugins installed

PE installs modules and plugins for normal operations.

-   Modules included with the software are installed on the master in `/opt/puppetlabs/puppet/modules`. Don't modify anything in this directory or add modules of your own. Instead, install non-default modules in `/etc/puppetlabs/code/environments/<environment>/modules`.
-   MCollective plugins are installed in `/opt/puppetlabs/mcollective/plugins/` on \*nix and in `<COMMON_APPDATA>``\PuppetLabs\mcollective\etc\plugins\mcollective` on Windows. If you are adding new plugins to agent nodes, distribute them with Puppet.

**Related information**  


[Adding actions and plugins to PE](adding_actions_and_plugins.md#)

## Configuration files installed

PE installs configuration files that you might need to interact with from time to time.

On \*nix nodes, configuration files live at `/etc/puppetlabs/puppet`.

On Windows nodes, configuration files live at `<COMMON_APPDATA>\PuppetLabs`. The location of this folder varies by Windows version; in 2008 and 2012, its default location is `C:\ProgramData\PuppetLabs\puppet\etc`.

The software's `confdir` is in the `puppet` subdirectory. This directory contains the `puppet.conf` file, `auth.conf`, and the SSL directory.

## Tools installed

PE installs several suites of tools to help you work with the major components of the software.

-   **Puppet tools** — Tools that control basic functions of the software such as `puppet master` and `puppet cert`.
-   **Client tools** — The pe-client-tools package collects a set of CLI tools that extend the ability for you to access services from the master or a workstation. This package includes:
    -   **Orchestrator** — The orchestrator is a set of interactive command line tools that provide the interface to the orchestration service. Orchestrator also enables you to enforce change on the environment level. Tools include `puppet job` and `puppet app`.
    -   **Puppet Access** — Users can generate tokens to authenticate their access to certain command line tools and API endpoints.
    -   **Code Manager CLI** — The `puppet-code` command allows you to trigger Code Manager from the command line to deploy your environments.
    -   **PuppetDB CLI** — This a tool for working with PuppetDB, including building queries and handling exports.
-   **MCollective tools** — Tools used to invoke simultaneous actions across a number of nodes. These tools are built on the MCollective framework and are accessed via the `mco` command.
-   **Module tool** — The module tool is used to access and create modules, which are reusable chunks of Puppet code users have written to automate configuration and deployment tasks. For more information, and to access modules, visit the Forge.
-   **Console** — The console is the web user interface for PE. The console provides tools to view and edit resources on your nodes, view reports and activity graphs, and more.

## Databases installed

PE installs several default databases, all of which use PostgreSQL as a database backend.

The PE PostgreSQL database includes these following databases.

|Database|Description|
|--------|-----------|
|pe-activity|Activity data from the classifier, including who, what, and when.|
|pe-classifier|Classification data, all node group information.|
|pe-puppetdb|PuppetDB data, including exported resources, catalogs, facts, and reports.|
|pe-rbac|RBAC data, including users, permissions, and AD/LDAP info.|
|pe-orchestrator|Orchestrator data, including details about job runs.|

Use the native PostgreSQL tools to perform database exports and imports. At a minimum, you should perform backups to a remote system nightly, or as dictated by your company policy.

## Services installed

PE installs several services used to interact with the software during normal operations.

|Service|Definition|
|-------|----------|
|pe-activemq|The ActiveMQ message server, which passes messages to the MCollective servers on agent nodes. Runs on servers with the master component.|
|pe-console-services|Manages and serves the console.|
|pe-puppetserver|Runs the master server, which manages the master component.|
|pe-nginx|Nginx, serves as a reverse-proxy to the console.|
|mcollective|The MCollective daemon, which listens for messages and invokes actions. Runs on every agent node.|
|puppet|\(on Enterprise Linux and Debian-based platforms\) Runs the agent daemon on every agent node.|
|pe-puppetdb, pe-postgresql|Daemons that manage and serve the database components. The pe-postgresql service is created only if the software installs and manages PostgreSQL.|
|pxp-agent|Runs the Puppet Execution Protocol agent process.|
|pe-orchestration-services|Runs the orchestration process.|

## User and group accounts installed

These are the user and group accounts installed.

|User|Definition|
|----|----------|
|peadmin|Invokes MCollective actions. The individual user account is the only user account intended for use in a login shell. This user exists on servers with the master component.|
|pe-puppet|Runs the master processes spawned by `pe-puppetserver`.|
|pe-webserver|Runs Nginx.|
|pe-activemq|Runs the ActiveMQ message bus used by MCollective.|
|pe-puppetdb|Has root access to the database.|
|pe-postgres|Has access to the `pe-postgreSQL` instance. Created only if the software installs and manages PostgreSQL.|
|pe-console-services|Runs the console process.|
|pe-orchestration-services|Runs the orchestration process.|

## Log files installed

The software distributed with PE generates log files that you can collect for compliance, or use for troubleshooting.

### Master logs

The master has these logs.

-   `/var/log/puppetlabs/puppetserver/puppetserver.log` — The master application logs its activity, including compilation errors and deprecation warnings, here.
-   `/var/log/puppetlabs/puppetserver/puppetserver-daemon.log` — This is where fatal errors or crash reports can be found.
-   `/var/log/puppetlabs/puppetserver/pcp-broker.log` — The log file for Puppet Communications Protocol brokers on compile masters.
-   `/var/log/puppetlabs/puppetserver/code-manager-access.log`
-   `/var/log/puppetlabs/puppetserver/file-sync-access.log`
-   `/var/log/puppetlabs/puppetserver/masterhttp.log`
-   `/var/log/puppetlabs/puppetserver/puppetserver-access.log`
-   `/var/log/puppetlabs/puppetserver/puppetserver.log`
-   `/var/log/puppetlabs/puppetserver/puppetserver-status.log`

### Agent logs

The locations of agent logs depend on the agent operating system.

On \*nix nodes, the agent service logs its activity to the syslog service. Your syslog configuration dictates where these messages are saved, but the default location is `/var/log/messages` on Linux, `/var/log/system.log` on macOS, and `/var/adm/messages` on Solaris.

On Windows nodes, the agent service logs its activity to the event log. You can view its logs by browsing the event viewer.

### ActiveMQ logs

ActiveMQ has these logs.

-   `/var/log/puppetlabs/activemq/wrapper.log`
-   `/var/log/puppetlabs/activemq/activemq.log`
-   `/var/log/puppetlabs/activemq/data/audit.log`

### MCollective logs

MCollective has these logs.

-   `/var/log/puppetlabs/mcollective.log` — Maintained by the MCollective service, which is installed on all nodes.
-   `/var/log/puppetlabs/mcollective-audit.log` — Exists on all nodes that have MCollective installed. Logs any MCollective actions run on the node, including information about the client that called the node

### Console and console services logs

The console and pe-console-services has these logs.

-   `/var/log/puppetlabs/nginx/error.log` — Contains errors related to nginx. Console errors that aren't logged elsewhere can be found in this log.
-   `/var/log/puppetlabs/nginx/access.log`
-   `/var/log/puppetlabs/console-services/console-services.log`
-   `/var/log/puppetlabs/console-services-access.log`
-   `/var/log/puppetlabs/console-services/console-services-api-access.log`
-   `/var/log/puppetlabs/console-services-daemon.log` — This is where fatal errors or crash reports can be found.

### Installer logs

The installer has these logs.

-   `/var/log/puppetlabs/installer/http.log` — Contains web requests sent to the installer. This log is present only on the machine from which a web-based installation was performed.
-   `/var/log/puppetlabs/installer/installer-<timestamp>.log` — Contains the operations performed and any errors that occurred during installation.
-   `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log` — Contains the contents of the last installer run.
-   `/var/log/puppetlabs/installer/<action_name>_<timestamp>_<run_description>.log` — Contains details about high availability command execution. Each action triggers multiple Puppet runs, some on the master, some on the replica, so there might be multiple log files for each command.


### Database logs

The database has these logs.

-   `/var/log/puppetlabs/puppetdb/puppetdb-access.log`
-   `/var/log/puppetlabs/puppetdb/puppetdb-status.log`
-   `/var/log/puppetlabs/puppetdb/puppetdb.log`
-   `/var/log/puppetlabs/postgresql/pgstartup.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Fri.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Mon.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Sat.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Sun.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Thu.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Tue.log`
-   `/var/log/puppetlabs/postgresql/postgresql-Wed.log`

### Orchestration logs

The orchestration service and related components have these logs.

-   `/var/log/puppetlabs/orchestration-services/orchestration-services.log`
-   `/var/log/puppetlabs/orchestration-services/orchestration-services-access.log`
-   `/var/log/puppetlabs/orchestration-services/orchestration-services-status.log`
-   `/var/log/puppetlabs/orchestration-services/orchestration-services-daemon.log` — This is where fatal errors or crash reports can be found.
-   `/var/log/puppetlabs/orchestration-services/pcp-broker.log` — The log file for Puppet Communications Protocol brokers on the master of masters.
-   `/var/log/puppetlabs/orchestration-services/pcp-broker-access.log`
-   `/var/log/puppetlabs/pxp-agent/pxp-agent.log` \(on \*nix\) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` \(on Windows\) — Contains the Puppet Execution Protocol agent log file.

## Certificates installed

During installation, the software generates and installs a number of SSL certificates so that agents and services can authenticate themselves.

These certs can be found at `/etc/puppetlabs/puppet/ssl/certs`.

Services that run on the master or console — for example, `pe-orchestration-services` and `pe-console-services` — use the agent certificate to authenticate.

|Cert|Definition|
|----|----------|
|<MASTER CERTNAME\>|Generated during install. In a monolithic install, this cert is used by PuppetDB and the console. This is the same value for the agent's certname that runs on the master. In a monolithic install, the agent on the console and PuppetDB share this certname. In a default monolithic or split install, this is also the CA certificate.|
|<CONSOLE CERTNAME\>|The certificate for the console, which is generated only if you have a split install. This is the same value for the agent's certname that runs on the console.|
|<PUPPETDB CERTNAME\>|The certificate for PuppetDB, which is generated only if you have a split install. This is the same value for the agent's certname that runs on PuppetDB.|
|pe-internal-mcollective-servers|A certificate generated on the master and shared to all agent nodes.|
|pe-internal-peadmin-mcollective-client|The certificate for the peadmin account on the master.|
|pe-internal-puppet-console-mcollective-client|The MCollective certificate for the console.|

