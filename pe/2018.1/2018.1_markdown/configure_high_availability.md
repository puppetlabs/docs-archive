---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Configure high availability

To configure high availability, you must provision and enable a replica to serve as backup during failovers. If your master is permanently disabled, you can then promote a replica.

## Before you begin

Apply [high availability system and software requirements](high_availability_overview.md#).

## About this task

**Tip:** High availability is configured and managed with `puppet infrastructure` commands, many of which require a valid admin RBAC token. For details about these commands, on the command line, run `puppet infrastructure help <ACTION>`, for example, `puppet infrastructure help provision`.  

**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

[System and software requirements](high_availability_overview.md#)

## Provision a replica

Provisioning a replica duplicates specific components and services from the master to the replica.

### Before you begin

Ensure you have a valid admin RBAC token. See [Generate a token using puppet-access](rbac_token_auth_intro.md#).

Ensure Code Manager is enabled and configured on your master.

### About this task

**Note:** While completing this task, the master is unavailable to serve catalog requests. Time completing this task accordingly.

### Procedure

1.  Ensure that the node you're provisioning as a replica is set to use the primary master as its Puppet Server.

    On the prospective replica node, in the `/etc/puppetlabs/puppet/puppet.conf` file's `main` section, set the server variable to the node name of the primary master. For example:

    ```
    [main]
    certname = <REPLICA NODE NAME> 
    server = <MASTER NODE NAME>
    
    ```

2.  On the primary master, as the root user, run `puppet infrastructure provision replica <REPLICA NODE NAME>`

    After the provision command completes, services begin syncing from the master to the replica. The amount of time the sync takes depends on the size of your PuppetDB and the capability of your hardware. Typical installations take 10-30 minutes. With large data sets, you can optionally do a manual PuppetDB replication to speed installation.

    **Note:** All `puppet infrastructure` commands must be run from a root session. Running with elevated privileges via `sudo puppet infrastructure` is not sufficient. Instead, start a root session by running `sudo su -`, and then run the `puppet infrastructure` command.

3.  \(Optional\) Verify that all services running on the primary master are also running on the replica:

    1.  From the primary master, run `puppet infrastructure status --verbose` to verify that the replica is available.

    2.  From any managed node, run `puppet agent -t --noop --server_list=<REPLICA HOSTNAME>`. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources.


### Results

### What to do next

When provisioning is complete, you must enable the replica to complete your HA configuration.

### Manually copy PuppetDB to speed replication

For large PuppetDB installations, you can speed initial replication by manually copying the database from the master to the replica. If you have already started automatic provisioning, you can manually copy your PuppetDB at any time during sync.

#### About this task

The size of your PuppetDB correlates with the number of nodes and resources in your Puppet catalogs. To optionally examine the size of your database, on the PuppetDB PostgreSQL node, run `sudo -u pe-postgres /opt/puppetlabs/server/bin/psql -c '\l+ "<DB_NAME>"'`.

**Note:** By default, <DB\_NAME\> is `pe-puppetdb`.

#### Procedure

1.  On the PuppetDB node, export the database:

    ```
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump --format=custom --compress=3 --file=<DUMP_OUTPUT> --dbname="<DB_NAME>"
    ```

2.  On the PuppetDB node, transfer the output using your preferred tool, such as SCP:

    ```
    scp -r <DUMP_OUTPUT> <REMOTE_USER>@<REPLICA_HOST>:<REPLICA_DUMP_OUTPUT>
    ```

3.  On the primary replica node, restore PuppetDB:

    ```
    sudo puppet resource service puppet ensure=stopped
    sudo puppet resource service pe-puppetdb ensure=stopped
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore --clean --jobs=<PROCESSOR_COUNT> --dbname="<DB_NAME>" <REPLICA_DUMP_OUTPUT>
    sudo puppet resource service puppet ensure=running
    sudo puppet resource service pe-puppetdb ensure=running
    sudo puppet agent -t
    ```


#### Results

After manual export and restore, PuppetDB automatically updates the replica with any changes that occurred on the master in the meantime.

## Enable a replica

Enabling a replica activates most of its duplicated services and components, and instructs agents and infrastructure nodes how to communicate in a failover scenario.

### Before you begin

-   Back up your classifier hierarchy, because enabling a replica alters classification.

-   Ensure you have a valid admin RBAC token. See [Generate a token using puppet-access](rbac_token_auth_intro.md#).


### About this task

**Note:** While completing this task, the master is unavailable to serve catalog requests. Time completing this task accordingly.

### Procedure

1.  On the primary master, as the root user, run `puppet infrastructure enable replica <REPLICA NODE NAME>`, then follow the prompts to instruct Puppet how to configure your deployment.

2.  Deploy updated configuration to nodes by running Puppet or waiting for the next scheduled Puppet run.

    **Note:** If you use the direct Puppet workflow, where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null and expired is null}'`

3.  Perform any tests you feel are necessary to verify that Puppet runs continue to work during failover. For example, to simulate an outage on the master:

    1.  Prevent the replica and a test node from contacting the master. For example, you might temporarily shut down the master or use `iptables` with drop mode.

    2.  Run `puppet agent -t` on the test node. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources. Runs may take longer than normal when in failover mode.

    3.  Reconnect the replica and test node.

4.  On the replica, in the `/etc/puppetlabs/puppet/puppet.conf` file's main section, remove the entire line where the server variable is set: `server = <MASTER NODE NAME>`

    When you enable HA, the `server` variable is superseded by the `server_list` variable. If you later promote a replica on which the server variable still exists and points to the old master, some commands might not function properly.

5.  If you've specified any tuning parameters for your master using the console, move them to Hiera instead.

    Using Hiera ensures configuration is applied to both your master and replica.


**Related information**  


[Back up your PE infrastructure](backup_and_restore_monolothic_installations_and_same-version_infrastructure.md#)

[Running Puppet on nodes](run_puppet_on_nodes.md#)

[Direct Puppet: a workflow for controlling change](direct_puppet_a_workflow_for_controlling_change.md#)

[Configure settings with Hiera](config_intro.md#)

[Classification changes in high availability installations](high_availability_overview.md#)

### Managing agent communication in geo-diverse installations

Typically, when you enable a replica using `puppet infrastructure enable replica`, the configuration tool automatically sets the same communication parameters for all agents. In geo-diverse installations, with load balancers or compile masters in multiple locations, you must manually configure agent communication settings so that agents fail over to the appropriate load balancer or compile master.

To skip automatically configuring which Puppet servers and PCP brokers agents communicate with, use the `--skip-agent-config` flag when you enable a replica, for example:

```
puppet infrastructure enable replica example.puppet.com --skip-agent-config
```

To manually configure which load balancer or compile master agents communicate with, use one of these options:

-   CSR attributes
    1.  For each node, include a CSR attribute that identifies the location of the node, for example `pp_region` or `pp_datacenter`.

    2.  Create child groups off of the **PE Agent** node group for each location.

    3.  In each child node group, include the `puppet_enterprise::profile::agent` module and set the `server_list` parameter to the appropriate load balancer or compile master hostname.

    4.  In each child node group, add a rule that uses the trusted fact created from the CSR attribute.

-   Hiera

    For each node or group of nodes, create a key/value pair that sets the `puppet_enterprise::profile::agent::server_list` parameter to be used by the **PE Agent** node group.

-   Custom method that sets the `server_list` parameter in `puppet.conf`.

## Promote a replica

If your master can’t be restored, you can promote the replica to master to establish the replica as the new, permanent master.

### Procedure

1.  Verify that the primary master is permanently offline.

    If the primary master comes back online during promotion, your agents can get confused trying to connect to two active masters.

2.  On the replica, as the root user, run `puppet infrastructure promote replica`

    Promotion can take up to the amount of time it took to install PE initially. Don’t make code or classification changes during or after promotion.

3.  When promotion is complete, update any systems or settings that refer to the old master, such as PE client tool configurations, Code Manager hooks, Razor brokers, and CNAME records.

4.  Deploy updated configuration to nodes by running Puppet or waiting for the next scheduled run.

    **Note:** If you use the direct Puppet workflow, where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null and expired is null}'`

5.  \(Optional\) Provision a new replica in order to maintain high availability.

    **Note:** Agent configuration must be updated before provisioning a new replica. If you re-use your old master’s node name for the new replica, agents with outdated configuration might use the new replica as a master before it’s fully provisioned.


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

[Direct Puppet: a workflow for controlling change](direct_puppet_a_workflow_for_controlling_change.md#)

## Enable a new replica using a failed master

After promoting a replica, you can use your old master as a new replica, effectively swapping the roles of your failed master and promoted replica.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### Procedure

1.  On your promoted replica logged in as root, run `puppet infrastructure run enable_ha_failover`, specifying these parameters:

    -   `host` — Hostname of the failed master. This node becomes your new replica.

    -   `caserver` — Hostname of the promoted replica that's serving as your new master.

        **Note:** If you're running PE version 2018.1.11 or newer, do not include the `caserver` parameter.

    -   `topology` — Architecture used in your environment, either `mono` or `mono-with-compile`

    -   `replication_timeout_secs` — Optional. The number of seconds allowed to complete provisioning and enabling of the new replica before the command fails.

    -   `tmpdir` — Optional. Path to a directory to use for uploading and executing temporary files.
    For example:

    ```
    puppet infrastructure run enable_ha_failover host=<FAILED_MASTER_HOSTNAME> caserver=<PROMOTED_REPLICA_HOSTNAME> topology=mono
    ```


### Results

The failed master is repurposed as a new replica.

**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

## Forget a replica

Forgetting a replica cleans up classification and database state, preventing degraded performance over time.

### Before you begin

Ensure you have a valid admin RBAC token. See [Generate a token using puppet-access](rbac_token_auth_intro.md#).

### About this task

Run the `forget` command whenever a replica node is destroyed, even if you plan to replace it with a replica with the same name.

### Procedure

1.  Verify that the replica to be removed is permanently offline.

2.  On the primary master, as the root user, run `puppet infrastructure forget <REPLICA NODE NAME>`

    The replica is decommissioned, the node is purged as an agent, and a Puppet run is completed on the master.


## Reinitialize a replica

If you encounter certain errors on your primary master replica after provisioning, you can reinitialize the replica. Reinitializing destroys and re-creates replica databases, as specified.

### Before you begin

Your primary master must be fully functional and the replica must be able to communicate with the primary master.

### About this task

CAUTION:

If you reinitialize a functional replica that you already enabled, the replica is unavailable to serve as backup in a failover during reinitialization.

Reinitialization is not intended to fix slow queries or intermittent failures. Reinitialize your replica only if it’s inoperational or you see replication errors.

### Procedure

1.  On the primary master replica, reinitialize databases as needed:

    -   All databases: `puppet infrastructure reinitialize replica`

    -   Specific databases: `puppet infrastructure reinitialize replica --db <DATABASE>` where `<DATABASE>` is `pe-activity`, `pe-classifier`, `pe-orchestrator`, or `pe-rbac`.

        **Important:** The replica must be running PE version 2018.1.8 or newer to reinitialize specific databases using the `--db` flag.

2.  Follow prompts to complete the reinitialization.


