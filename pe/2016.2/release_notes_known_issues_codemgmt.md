---
layout: default
title: "Code management known issues"
canonical: "/pe/latest/release_notes_known_issues_codemgmt.html"
---

[code_mgr_custom]: ./code_mgr_custom.html

This page lists known issues for Code Manager, r10k, and file sync in Puppet Enterprise.

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

## **New:** Code Manager and r10k can develop bloated caches 
 
If you use either r10k with the Rugged provider or Code Manager, repository pruning is not supported. This means that if you use many short-lived branches, over time the local cache can become bloated and take up significant disk space.
 
If you encounter this issue, run `git-gc` periodically on any cached repo that is using a large amount of disk space in the cachedir. If you use r10k, you can alternately use the [shellgit provider](https://docs.puppet.com/pe/latest./r10k_custom.html#provider), which automatically garbage collects the repos according to the normal Git CLI rules. <!--RK-234-->

## **New:** Code Manager and r10k do not identify the default branch for module repositories  

When you use Code Manager or r10k to deploy modules from a Git source, the default branch of the source repository is always assumed to be 'master'. If the module repository uses a default branch that is *not* 'master', this causes an error. To work around this issue, specify the default branch with the [`ref:`](./cmgmt_puppetfile.html#declare-a-git-repo-as-a-module) key in your Puppetfile. <!--RK-261-->

## Code Manager webhook does not delete branches as expected

If you delete a branch from your control repository, the Code Manager webhook deployment does not immediately delete that environment from the Puppet master's staging or live code directories.

Code Manager will delete the environment when it deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually:

```
puppet-code deploy --all --wait
```

## File sync fails if a .git directory in the live code dir

If you are upgrading from r10k to Code Manager, file sync fails if it encounters .git directories, which r10k creates, in the live code directory. Remove all .git directories from your live code directory (`/etc/puppetlabs/code`) before setting up Code Manager. <!--PE-15241-->

## Environments restored with Code Manager are still considered deleted by the node classifier

If you restore an environment containing the same classes that it formerly had, the node classifier still treats that environment as deleted. This issue might affect you if you delete the live code directory and then restore code by triggering a deployment with Code Manager. Note that as of PE 2016.1, we do not recommend removing the live code directory while setting up Code Manager.

If you encounter this issue, you can restore a single environments (for example, an environment called "Mirror") by running:

```
sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql -d 'pe-classifier' -c \"UPDATE environments SET deleted = 'f' WHERE name = 'Mirror';\""
```
To restore all environments, run:

```
sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql -d 'pe-classifier' -c \"UPDATE environments SET deleted = 'f' WHERE deleted = 't';\""
```

## Puppet Server crashes if file sync cannot write to the live code directory

If the live code directory contains content that file sync didn’t expect to find there (for example, someone has made changes directly to the live code directory), Puppet Server crashes. <!--PE-15066--> <!--CODEMGMT-734-->

The following error appears in the puppetserver.log

```
2016-05-05 11:57:06,042 ERROR [clojure-agent-send-off-pool-0] [p.e.s.f.file-sync-client-core] Fatal error during file sync, requesting shutdown.
org.eclipse.jgit.api.errors.JGitInternalException: Could not delete file /etc/puppetlabs/code/environments/development
        at org.eclipse.jgit.api.CleanCommand.call(CleanCommand.java:138) ~[puppet-server-release.jar:na]
```

To recover from this error:

1. Delete the environments in code dir: `find /etc/puppetlabs/code -mindepth 1 -delete`.
2. Start the `pe-puppetserver` service: `puppet resource service pe-puppetserver ensure=running`
3. Trigger a Code Manager run by your usual method.

## Code Manager webhook authentication for GitLab

Prior to GitLab 8.5.0, GitLab had a character limit on webhooks, so the entire Code Manager authentication token could not be passed into GitLab webhooks. If you are using a GitLab version earlier that 8.5.0, either upgrade to the current GitLab version or turn off Code Manager webhook authentication [via Hiera][code_mgr_custom] with the `authenticate_webhook` parameter.

## File sync error causes `pe-puppetserver` service to shut down due to incorrect `pe-puppet` owner and group permissions

During an r10k deployment, if the `pe-puppet` user does not have permissions to delete files in the environment directory, a file sync error will cause the `pe-puppetserver` service to shut down during deployment.

This issue can be fixed by setting permissions in the environment directory to owner `pe-puppet` and group `pe-puppet` with the following command: `chown -R pe-puppet:pe-puppet /etc/puppetlabs/code/environments`.

