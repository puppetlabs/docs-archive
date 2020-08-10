---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Configure disaster recovery

To configure disaster recovery, you must provision a replica to serve as backup during failovers. If your master is permanently disabled, you can then promote a replica.

Apply [disaster recovery system and software requirements](dr_overview.md#).

**Tip:** The `puppet infrastructure` commands, which are used to configure and manage disaster recovery, require a valid admin RBAC token and must be run from a root session. Running with elevated privileges via `sudo puppet infrastructure` is not sufficient. Instead, start a root session by running `sudo su -`, and then run the `puppet infrastructure` command. For details about these commands, run `puppet infrastructure help <ACTION>`, for example, `puppet infrastructure help provision`.  

**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

## Provision and enable a replica

Provisioning a replica duplicates specific components and services from the master to the replica. Enabling a replica activates most of its duplicated services and components, and instructs agents and infrastructure nodes how to communicate in a failover scenario.

-   Ensure you have a valid admin RBAC token.

-   Move any tuning parameters that you set for your master using the console to Hiera. Using Hiera ensures configuration is applied to both your master and replica.

-   Back up your classifier hierarchy, because enabling a replica alters classification.


**Note:** While completing this task, the master is unavailable to serve catalog requests. Time completing this task accordingly.

1.  Ensure that the node you're provisioning as a replica is set to use the master as its Puppet Server.

    On the prospective replica node, in the `/etc/puppetlabs/puppet/puppet.conf` file's `main` section, set the server variable to the node name of the master. For example:

    ```
    [main]
    certname = <REPLICA NODE NAME> 
    server = <MASTER NODE NAME>
    ```

2.  On the master, as the root user, run `puppet infrastructure provision replica <REPLICA NODE NAME> --enable`

    **Note:** In installations with compile masters, use the `--skip-agent-config` flag with the `--enable` option if you want to:

    -   Upgrade a replica without needing to run Puppet on all agents.
    -   Add disaster recovery to an installation without modifying the configuration of existing load balancers.
    -   Manually configure which load balancer agents communicate with in geo-diverse installations. See [Managing agent communication in geo-diverse installations](dr_configure.md#).
3.  Copy your secret key file from the master to the replica. The path to the secret key file is `/etc/puppetlabs/orchestration-services/conf.d/secrets/keys.json`.

    **Important:** If you do not copy your secret key file onto your replica, the replica generates a new secret key when you promote it. The new key prevents you from accessing credentials for your agentless nodes or running tasks and plans on agentless nodes.

4.  Verify that all services running on the master are also running on the replica:

    1.  From the master, run `puppet infrastructure status --verbose` to verify that the replica is available.

    2.  From any managed node, run `puppet agent -t --noop --server_list=<REPLICA HOSTNAME>`. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources.

5.  Deploy updated configuration to agents by running Puppet, or wait for the next scheduled Puppet run.

    If you used the `--skip-agent-config` option, you can skip this step.

    **Note:** If you use the direct Puppet workflow, where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null and expired is null}'`

6.  Perform any tests you feel are necessary to verify that Puppet runs continue to work during failover. For example, to simulate an outage on the master:

    1.  Prevent the replica and a test node from contacting the master. For example, you might temporarily shut down the master or use `iptables` with drop mode.

    2.  Run `puppet agent -t` on the test node. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources. Runs might take longer than normal when in failover mode.

    3.  Reconnect the replica and test node.


**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

[Back up your infrastructure](backing_up_and_restoring_pe.md#)

[Running Puppet on nodes](run_puppet_on_nodes.md#)

[Direct Puppet: a workflow for controlling change](direct_puppet_a_workflow_for_controlling_change.md#)

[Configure settings with Hiera](config_intro.md#)

### Managing agent communication in geo-diverse installations

Typically, when you enable a replica using `puppet infrastructure enable replica`, the configuration tool automatically sets the same communication parameters for all agents. In geo-diverse installations, with load balancers or compilers in multiple locations, you must manually configure agent communication settings so that agents fail over to the appropriate load balancer or compiler.

To skip automatically configuring which Puppet servers and PCP brokers agents communicate with, use the `--skip-agent-config` flag when you provision and enable a replica, for example:

```
puppet infrastructure provision replica example.puppet.com --enable --skip-agent-config
```

To manually configure which load balancer or compiler agents communicate with, use one of these options:

-   CSR attributes
    1.  For each node, include a CSR attribute that identifies the location of the node, for example `pp_region` or `pp_datacenter`.

    2.  Create child groups off of the **PE Agent** node group for each location.

    3.  In each child node group, include the `puppet_enterprise::profile::agent` module and set the `server_list` parameter to the appropriate load balancer or compiler hostname.

    4.  In each child node group, add a rule that uses the trusted fact created from the CSR attribute.

-   Hiera

    For each node or group of nodes, create a key/value pair that sets the `puppet_enterprise::profile::agent::server_list` parameter to be used by the **PE Agent** node group.

-   Custom method that sets the `server_list` parameter in `puppet.conf`.

## Promote a replica

If your master can’t be restored, you can promote the replica to master to establish the replica as the new, permanent master.

1.  Verify that the master is permanently offline.

    If the master comes back online during promotion, your agents can get confused trying to connect to two active masters.

2.  On the replica, as the root user, run `puppet infrastructure promote replica`

    Promotion can take up to the amount of time it took to install PE initially. Don’t make code or classification changes during or after promotion.

3.  When promotion is complete, update any systems or settings that refer to the old master, such as PE client tool configurations, Code Manager hooks, Razor brokers, and CNAME records.

4.  Deploy updated configuration to nodes by running Puppet or waiting for the next scheduled run.

    **Note:** If you use the direct Puppet workflow, where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null and expired is null}'`

5.  Provision a new replica in order to maintain disaster recovery.

    **Note:** Agent configuration must be updated before provisioning a new replica. If you re-use your old master’s node name for the new replica, agents with outdated configuration might use the new replica as a master before it’s fully provisioned.


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

[Direct Puppet: a workflow for controlling change](direct_puppet_a_workflow_for_controlling_change.md#)

## Enable a new replica using a failed master

After promoting a replica, you can use your old master as a new replica, effectively swapping the roles of your failed master and promoted replica.

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

You must be able to reach the failed master via SSH from the current master.

1.  On your promoted replica, as the root user, run `puppet infrastructure run enable_ha_failover`, specifying these parameters:

    -   `host` — Hostname of the failed master. This node becomes your new replica.

    -   `topology` — Architecture used in your environment, either `mono` \(standard\) or `mono-with-compile` \(large\).

    -   `replication_timeout_secs` — Optional. The number of seconds allowed to complete provisioning and enabling of the new replica before the command fails.

    -   `tmpdir` — Optional. Path to a directory to use for uploading and executing temporary files.
    For example:

    ```
    puppet infrastructure run enable_ha_failover host=<FAILED_MASTER_HOSTNAME> topology=mono
    ```


The failed master is repurposed as a new replica.

## Forget a replica

Forgetting a replica cleans up classification and database state, preventing degraded performance over time.

Ensure you have a valid admin RBAC token.

Run the `forget` command whenever a replica node is destroyed, even if you plan to replace it with a replica with the same name.

1.  Verify that the replica to be removed is permanently offline.

2.  On the master, as the root user, run `puppet infrastructure forget <REPLICA NODE NAME>`

3.  Delete your secret key file from the replica because leaving sensitive information on a replica poses a security risk. The path to the secret key file is `/etc/puppetlabs/orchestration-services/conf.d/secrets/keys.json`


The replica is decommissioned, the node is purged as an agent, secret key information is deleted, and a Puppet run is completed on the master.

**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

## Reinitialize a replica

If you encounter certain errors on your replica after provisioning, you can reinitialize the replica. Reinitializing destroys and re-creates replica databases, as specified.

Your master must be fully functional and the replica must be able to communicate with the master.

CAUTION:

If you reinitialize a functional replica that you already enabled, the replica is unavailable to serve as backup in a failover during reinitialization.

Reinitialization is not intended to fix slow queries or intermittent failures. Reinitialize your replica only if it’s inoperational or you see replication errors.

1.  On the replica, as the root user, reinitialize databases as needed:

    -   All databases: `puppet infrastructure reinitialize replica`

    -   Specific databases: `puppet infrastructure reinitialize replica --db <DATABASE>` where `<DATABASE>` is `pe-activity`, `pe-classifier`, `pe-orchestrator`, or `pe-rbac`.

2.  Follow prompts to complete the reinitialization.


