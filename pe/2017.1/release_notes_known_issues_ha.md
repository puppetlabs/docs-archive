---
layout: default
title: "High availability known issues"
canonical: "/pe/latest/release_notes_known_issues_ha.html"
---

This page lists known issues for high availability in Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Provisioning a replica node that's connected to a compile master causes Puppet runs to fail

If you try to provision a replica node that's connected to a compile master, rather than a primary master, it can cause an error:

~~~
Failure during provision command during the puppet agent run on replica 2:
Failed to generate additional resources using 'eval_generate': Error 500 on SERVER: Server Error: Not authorized to call search on /file_metadata/pe_modules with {:rest=>"pe_modules", :links=>"manage", :recurse=>true, :source_permissions=>"ignore", :checksum_type=>"md5"}
Source: /Stage[main]/Puppet_enterprise::Profile::Primary_master_replica/File[/opt/puppetlabs/server/share/installer/modules]File: /opt/puppetlabs/puppet/modules/puppet_enterprise/manifests/profile/primary_master_replica.ppLine: 64
~~~

As a workaround, on the replica you want to provision, edit `/etc/puppetlabs/puppet.conf` so that the `server` and `server_list` settings use a primary master, rather than a compile master.

## Executing `enable` command immediately after `provision` can cause error

If you have scripted or automated the process to set up a replica, the `enable` command can sometimes fail with the error, 'ERROR [p.p.puppet] Received a PCP error message due to message 7f7ed934-d2ed-4d64-8b5c-e2cb48cb2227 (blocking request for 'status query')'.

To resolve the issue, run `puppet agent -t` on both the master and replica nodes, then try the `enable` command again. There is a fix planned for this issue in a future release.

## Running `puppet infrastructure configure` fails on promoted replica

The `/etc/puppetlabs/enterprise` directory contains configuration files used
during the installation and upgrade of Puppet Enterprise. These files are not
automatically copied to a replica; this poses no issue during normal operation,
but may cause problems if you need to upgrade Puppet Enterprise on the replica
after it is promoted.

To avoid potential issues:

1. Back up the `/etc/puppetlabs/enterprise` directory of the primary master.
2. When you promote a replica, copy the backup into place.

This directory will be automatically copied to the replica in a future release
of Puppet Enterprise.

## After HA enablement, both `server` and `server_list` are set

When the puppet agent configuration file contains settings for both `server` and `server_list`, a warning is printed. This can occur after enabling a replica in a high availability Puppet Enterprise configuration. The warning can be ignored, and will be downgraded in a future release.

If you want to hide the warning, remove the `server` setting from the agent configuration, leaving only `server_list`.

## Running `enable` starts an orchestrator run that fails

When provisioning and enabling a replica, the orchestrator is used to run Puppet on different groups of nodes. If a groups of nodes is empty, the tool prints the text, "nothing to do" and the job is marked as `failed` in the output of `puppet job show`. This is expected, and does not indicate a problem.

## RBAC displays permission denied error

On a primary master replica, error messages similar to this may appear in the console services log file:


	2016-11-11 17:34:22,824 [pool-4-thread-2] ERROR [p.c.class-updater] Encountered an unexpected exception while trying to synchronize classes from the Puppet Master:
	org.postgresql.util.PSQLException: ERROR: permission denied for relation environment_classes


These errors may be ignored.

## Latency over WAN can cause failure

If the master and replica communicate over a slow, high latency, or lossy connection, the `provision` and `enable` commands can fail. If this happens, try re-running the command that failed.



