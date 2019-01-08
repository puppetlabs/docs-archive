---
layout: default
title: "Code management known issues"
canonical: "/pe/latest/release_notes_known_issues_codemgmt.html"
---

[code_mgr_custom]: ./code_mgr_custom.html

This page lists known issues for Code Manager, r10k, and file sync in Puppet Enterprise 2016.1.

## **New:** Puppet Services status monitor causes frequent `file-sync-storage-service` timeouts

In some deployments, the `file-sync-storage-service` endpoint times out and posts a `Status callback timed out` error and a large stack trace to the Puppet Server log: 

```
2016-11-29 16:21:28,250 ERROR [clojure-agent-send-off-pool-139] [p.t.s.s.status-core] Status callback timed out, shutting down background task
2016-11-29 16:21:28,251 ERROR [clojure-agent-send-off-pool-139] [p.t.s.s.status-core] #error {
 :cause nil
 :via
 [{:type java.util.concurrent.CancellationException
   :message nil
   :at [java.util.concurrent.FutureTask report FutureTask.java 121]}]
 :trace
 [[java.util.concurrent.FutureTask report FutureTask.java 121]
```

The frequent calls made by the Puppet Services status monitor to the endpoint might cause this timeout. This error shouldn't cause any issues, and can be safely disregarded. <!--CODEMGMT-950--> 

## **New:** Environments restored with Code Manager are still considered deleted by the node classifier

If you restore an environment containing the same classes that it formerly had, the node classifier still treats that environment as deleted. This issue might affect you if you delete the live code directory and then restore code by triggering a deployment with Code Manager. Note that as of PE 2016.1, we do not recommend removing the live code directory while setting up Code Manager.

If you encounter this issue, you can restore a single environments (for example, an environment called "Mirror") by running:

```
sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql -d 'pe-classifier' -c \"UPDATE environments SET deleted = 'f' WHERE name = 'Mirror';\""
```
To restore all environments, run:

```
sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql -d 'pe-classifier' -c \"UPDATE environments SET deleted = 'f' WHERE deleted = 't';\""
```

## **New:** After an error during the initial run of file sync, Puppet Server won't start

The first time file sync runs on a master, an error can occur that prevents Puppet Server from starting. <!--PE-16144--> To work around this issue:

1. Stop the pe-puppetserver service.
2. Locate the data-dir variable in `/etc/puppetlabs/puppetserver/conf.d/file-sync.conf`.
3. Remove the directory.
4. Start the pe-puppetserver service.

## **New:** Puppet Server crashes if file sync cannot write to the live code directory

If the live code directory contains content that file sync didn’t expect to find there (for example, someone has made changes directly to the live code directory), Puppet Server crashes. <!--PE-15066-->

The following error appears in the puppetserver.log

```
2016-05-05 11:57:06,042 ERROR [clojure-agent-send-off-pool-0] [p.e.s.f.file-sync-client-core] Fatal error during file sync, requesting shutdown.
org.eclipse.jgit.api.errors.JGitInternalException: Could not delete file /etc/puppetlabs/code/environments/development
        at org.eclipse.jgit.api.CleanCommand.call(CleanCommand.java:138) ~[puppet-server-release.jar:na]
```

To recover from this error:

1. Delete the environments in code dir: `rm -rf /etc/puppetlabs/code/*`
2. Start the `pe-puppetserver` service: `puppet resource service pe-puppetserver ensure=running`
3. Trigger a Code Manager run by your usual method.

## Code Manager webhook authentication for GitLab

Prior to GitLab 8.5.0, GitLab had a character limit on webhooks, so the entire Code Manager authentication token could not be passed into GitLab webhooks. If you are using a GitLab version earlier that 8.5.0, either upgrade to the current GitLab version or turn off Code Manager webhook authentication [via Hiera][code_mgr_custom] with the `authenticate_webhook` parameter.

### File sync overwrites hiera.yaml

When Code Manager runs, file sync overwrites the `hiera.yaml` file located in the codedir (`/etc/puppetlabs/code`). If you manage your `hiera.yaml` with Puppet, move this file to a location outside of the codedir. We suggest `/etc/puppetlabs/puppet/hiera.yaml`. <!--PE-13367--> <!--HI-490-->