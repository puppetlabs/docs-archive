# High availability known issues

These are the known issues for high availability in this release.

## Provisioning a replica node that's connected to a compile master causes a run to fail

If you try to provision a replica node that's connected to a compile master, rather than a primary master, it can cause an error:

```
Failure during provision command during the puppet agent run on replica 2:
Failed to generate additional resources using 'eval_generate': Error 500 on SERVER: Server Error: Not authorized to call search on /file_metadata/pe_modules with {:rest=>"pe_modules", :links=>"manage", :recurse=>true, :source_permissions=>"ignore", :checksum_type=>"md5"}
Source: /Stage[main]/Puppet_enterprise::Profile::Primary_master_replica/File[/opt/puppetlabs/server/share/installer/modules]File: /opt/puppetlabs/puppet/modules/puppet_enterprise/manifests/profile/primary_master_replica.ppLine: 64
```

As a workaround, on the replica you want to provision, edit `/etc/puppetlabs/puppet.conf` so that the `server` and `server_list` settings use a primary master, rather than a compile master.

## Executing enable command immediately after provisioning can cause error

If you scripted or automated the process to set up a replica, the `enable` command can sometimes fail with the error:

```
ERROR [p.p.puppet] Received a PCP error message due to message 7f7ed934-d2ed-4d64-8b5c-e2cb48cb2227 (blocking request for ‘status query’)
```

To resolve the issue, run `puppet agent -t` on both the master and replica nodes, then try the `enable` command again.

## Running `puppet infrastructure configure` fails on a promoted replica

The `/etc/puppetlabs/enterprise` directory contains configuration files used during installation and upgrade. These files are not automatically copied to a replica; this poses no issue during normal operation, but might cause problems if you need to upgrade on the replica after it is promoted.

To avoid potential issues:

1.  Back up the `/etc/puppetlabs/enterprise` directory of the primary master.
2.  When you promote a replica, copy the backup into place.

## After HA enablement, both `server` and `server_list` are set

When the agent configuration file contains settings for both `server` and `server_list`, a warning appears. This warning can occur after enabling a replica in a high availability configuration. You can ignore the warning, or hide it by removing the `server` setting from the agent configuration, leaving only `server_list`.

## Running `enable` starts an orchestrator run that fails

When provisioning and enabling a replica, the orchestrator is used to run Puppet on different groups of nodes. If a group of nodes is empty, the tool reports that there's nothing for it to do and the job is marked as `failed` in the output of `puppet job show`. This is expected, and doesn't indicate a problem.

## Latency over WAN can cause failure

If the master and replica communicate over a slow, high latency, or lossy connection, the `provision` and `enable` commands can fail. If this happens, try re-running the command.

