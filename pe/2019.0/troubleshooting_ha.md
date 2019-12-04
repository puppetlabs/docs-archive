# Troubleshooting high availability

If high availability commands fail, check for these issues.

## Latency over WAN

If the master and replica communicate over a slow, high latency, or lossy connection, the `provision` and `enable` commands can fail.

If this happens, try re-running the command.

## Replica is connected to a compile master instead of a primary master

The `provision` command triggers an error if you try to provision a replica node that's connected to a compile master. The error is similar to the following:

```
Failure during provision command during the puppet agent run on replica 2:
Failed to generate additional resources using 'eval_generate': Error 500 on SERVER: Server Error: Not authorized to call search on /file_metadata/pe_modules with {:rest=>"pe_modules", :links=>"manage", :recurse=>true, :source_permissions=>"ignore", :checksum_type=>"md5"}
Source: /Stage[main]/Puppet_enterprise::Profile::Primary_master_replica/File[/opt/puppetlabs/server/share/installer/modules]File: /opt/puppetlabs/puppet/modules/puppet_enterprise/manifests/profile/primary_master_replica.ppLine: 64
```

On the replica you want to provision, edit `/etc/puppetlabs/puppet.conf` so that the `server` and `server_list` settings use a primary master, rather than a compile master.

## Both `server` and `server_list` are set in the agent configuration file

When the agent configuration file contains settings for both `server` and `server_list`, a warning appears. This warning can occur after enabling a replica in a high availability configuration. You can ignore the warning, or hide it by removing the `server` setting from the agent configuration, leaving only `server_list`.

## Node groups are empty

When provisioning and enabling a replica, the orchestrator is used to run Puppet on different groups of nodes. If a group of nodes is empty, the tool reports that there's nothing for it to do and the job is marked as `failed` in the output of `puppet job show`. This is expected, and doesn't indicate a problem.

