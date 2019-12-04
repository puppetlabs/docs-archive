---
layout: default
title: "Configure high availability"
canonical: "/pe/latest/ha_configure.html"

---

Configuring high availability involves provisioning and enabling a replica to serve as backup during failovers. If your master is permanently disabled, you can then promote a replica.

For details about upgrading with high availability enabled, see [Upgrade a high availability installation](./upgrade_mono.html#upgrade-a-high-availability-installation).

> **Before you begin:** You must apply the [high availability system and software requirements](./ha_overview.html#system-and-software-requirements) before configuring HA.

## Provision a replica

Provisioning a replica duplicates specific components and services from the master to the replica.

For details about which components and services run on the replica, see [High availability architecture](./ha_overview.html#high-availability-architecture).

**Note:** While provisioning a replica, the master is unavailable to serve catalog requests. Time running the `provision` command accordingly.

1. Ensure that the node you're provisioning as a replica is set to use the primary master as its Puppet server.

   On the prospective replica node, in the `/etc/puppetlabs/puppet/puppet.conf` file's `main` section, set the `server` variable to the node name of the primary master. For example:

   ``` puppet
   [main]
   certname = <REPLICA NODE NAME>
   server = <MASTER NODE NAME>
   ```

2. On the primary master, as the root user, run `puppet infrastructure provision replica <REPLICA NODE NAME>`.

   After the provision command completes, services begin syncing from the master to the replica. The amount of time the sync takes depends on the size of your PuppetDB and the capability of your hardware. Typical installations might take 10-30 minutes. With large data sets, you can optionally do a [manual PuppetDB replication](#manually-copy-puppetdb-to-speed-replication) to speed installation.


3. (Optional) Verify that all services running on the primary master are also running on the replica:

   1. From the primary master, run `puppet infrastructure status --verbose` to verify that the replica is available.

   2. From a managed node, run `puppet agent -t --noop --server_list=<REPLICA HOSTNAME>`. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources.


### Manually copy PuppetDB to speed replication

For large PuppetDB installations, you can speed initial replication by manually copying the database from the master to the replica. If you already started automatic provisioning, you can manually copy your PuppetDB at any time during sync.

The size of your PuppetDB correlates with the number of nodes and resources in your Puppet catalogs. To optionally examine the size of your database, on the PuppetDB PostgreSQL node, run `sudo -u pe-postgres /opt/puppetlabs/server/bin/psql -c '\l+ “<DB_NAME>"'`.

**Note:** By default, the &lt;DB_NAME&gt; is `pe-puppetdb`.

1. On the PuppetDB node, export the database: `sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump --format=custom --compress=3 --file=<DUMP_OUTPUT> --dbname="<DB_NAME>".

2. On the PuppetDB node, transfer the output using your preferred tool, such as SCP: `scp -r <DUMP_OUTPUT> <REMOTE_USER>@<REPLICA_HOST>:<REPLICA_DUMP_OUTPUT>`.

3. On the primary replica node, restore PuppetDB:

   ``` puppet
   sudo puppet resource service puppet ensure=stopped
   sudo puppet resource service pe-puppetdb ensure=stopped
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore --clean --jobs=<PROCESSOR_COUNT> --dbname="<DB_NAME>" <REPLICA_DUMP_OUTPUT>
   sudo puppet resource service puppet ensure=started
   sudo puppet resource service pe-puppetdb ensure=started
   sudo puppet agent -t
   ```

   After manual export and restore, PuppetDB automatically updates the replica with any changes that occurred on the master in the meantime.

## Enable a replica

Enabling a replica activates most of its duplicated services and components, and instructs agents and infrastructure nodes how to communicate in a failover scenario.

**Note:** While enabling a replica, the master is unavailable to serve catalog requests. Time running the `enable` command accordingly.

> **Before you begin:** [Back up](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files) your classifier hierarchy, because enabling a replica [alters classification](./ha_overview.html#classification-changes-in-high-availability-installations).

1. On the primary master, as the root user, run `puppet infrastructure enable replica <REPLICA NODE NAME>`.

   Follow the prompts to instruct Puppet how to configure your deployment.

2. Deploy updated configuration to nodes by [running Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) or waiting for the next scheduled Puppet run.

   **Note:** If you use the [direct Puppet workflow](./direct_puppet_workflow.html), where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null}'`.


3. (Optional) Perform any tests you feel are necessary to verify that Puppet runs continue to work during failover. For example, to simulate an outage on the master:

   1. Prevent the replica and a test node from contacting the master. For example, you might temporarily shut down the master or use `iptables` with drop mode.
   2. Run `puppet agent -t` on the test node. If the replica is correctly configured, the Puppet run succeeds and shows no changed resources. Runs may take longer than normal when in failover mode.
   3. Reconnect the replica and test node.

4. On the replica, in the `/etc/puppetlabs/puppet/puppet.conf` file's main section, remove the entire line where the server variable is set: `server = <MASTER NODE NAME>`.

   When you enable HA, the `server` variable is superseded by the `server_list` variable. If you later promote a replica on which the server variable still exists and points to the old master, some Puppet commands might not function properly.

## Promote a replica

If your master can’t be restored, you can promote the replica to master to establish the replica as the new, permanent master.

1. Verify that the primary master is permanently offline.

   If the primary master comes back online during promotion, your agents can get confused trying to connect to two active masters.

2. On the primary master replica, as the root user, run `puppet infrastructure promote replica`.

   Promotion can take up to the amount of time it took to install PE initially. Don’t make code or classification changes during or after promotion.


3. When promotion is complete, update any systems or settings that refer to the old master, such as PE client tool configurations, Code Manager hooks, Razor brokers, and CNAME records.

4. Deploy updated configuration to nodes by [running Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) or waiting for the next scheduled Puppet run.

   **Note:** If you use the [direct Puppet workflow](./direct_puppet_workflow.html), where agents use cached catalogs, you must manually deploy the new configuration by running `puppet job run --no-enforce-environment --query 'nodes {deactivated is null}'`.


5. (Optional) Provision a new replica in order to maintain high availability.

   > **Important:** Agent configuration must be updated before provisioning a new replica. If you re-use your old master’s node name for the new replica, agents with outdated configuration might use the new replica as a master before it’s fully provisioned.


## Forget a replica

Run the `forget` command whenever a replica node is destroyed, even if you plan to replace it with a replica with the same name. Forgetting a replica cleans up classification and database state, preventing degraded performance over time.

1. Verify that the replica to be removed is permanently offline.

2. On the primary master, as the root user, run `puppet infrastructure forget <REPLICA NODE NAME>`.

3. Revoke certificates for the deleted replica node and deactivate it in PuppetDB by running `puppet node purge <REPLICA NODE NAME>`.


* * *
