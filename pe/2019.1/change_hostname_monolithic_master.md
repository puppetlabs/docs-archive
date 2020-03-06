---
author: Eamonn Smith <eamonn.smith@puppet.com\>
---

# Change the hostname of a monolithic master

To change the hostnames assigned to your PE infrastructure nodes requires updating the corresponding PE certificate names. You might have to update your master hostname, for example, when migrating to a new PE installation, or after an organizational change such as a change in company name.

## Before you begin

Download and install the [puppetlabs-support\_tasks](https://forge.puppet.com/puppetlabs/support_tasks) module.

Make sure you are using the latest version of the support\_tasks module.

## About this task

To update the hostname of your master:

## Procedure

1.  On the master, set the new hostname by running the following command:

    ```
    hostnamectl set-hostname newhostname.example.com
    ```

2.  Make sure the `hostname -f` command returns the new fully qualified hostname, and that it resolves to the same IP address as the old hostname, by adding an entry for the new hostname in `/etc/hosts`:

    ```
    <IP address> <newhostname.example.com> <oldhostname.example.com> …
    ```

3.  Run a task for changing the host name against the old certificate name on the master, using one of the following methods:

    -   Using Bolt:

        ```
        bolt task run support_tasks::st0263_rename_pe_master -n $(puppet config print certname) --modulepath="/etc/puppetlabs/code/environments/production/modules"
        ```

        **Note:** When running the task, Bolt must be using the default SSH transport, rather than the PCP protocol, to avoid errors when services are restarted.

    -   Using the command line:

        ```
        puppet task run support_tasks::st0263_rename_pe_master -n $(puppet config print certname)
        ```

    The task restarts all Puppet services, which causes a connection error. You can ignore the error while the task continues to run in the background. To check if the task is complete, tail `/var/log/messages`. When the output from the `puppet agent -t` command is displayed in the system log, the task is complete. For example:

    ```
    # tail /var/log/messages
    Aug 15 09:08:28 oldhostname systemd: Reloading pe-orchestration-services Service.
    Aug 15 09:08:29 oldhostname systemd: Reloaded pe-orchestration-services Service.
    Aug 15 09:08:29 oldhostname puppet-agent[4780]: (/Stage[main]/Puppet_enterprise::Profile::Orchestrator/Puppet_enterprise::Trapperkeeper::Pe_service[orchestration-services]/Service[pe-orchestration-services]) Triggered 'refresh' from 1 event
    Aug 15 09:08:34 oldhostname puppet-agent[4780]: Applied catalog in 19.16 seconds
    ```


