---
layout: default
title: "Code management known issues"
canonical: "/pe/latest/release_notes_known_issues_codemgmt.html"
---

[code_mgr_custom]: ./code_mgr_custom.html

This page lists known issues for Code Manager, r10k, and file sync in Puppet Enterprise.

## **New:** Module depreciation check causes increase in deployment time 

In r10k versions 2.5.3 and 2.5.4, a module depreciation check is performed on every redeploy, causing a significant increase in deployment time. To work around this issue, upgrade to r10k version 2.5.5 by running the following commands:

```
/opt/puppetlabs/puppet/bin/gem uninstall r10k
/opt/puppetlabs/puppet/bin/gem install r10k -v 2.5.5
```
<!--RK-294-->

## **New:** Timeouts when using `--wait` with large deployments or geographically dispersed compile masters

As the `--wait` flag now deploys code to all compile masters before returning results, some Puppet Enterprise deployments with a large node count or compile masters spread across a large geographic area might experience a timeout. Work around this issue by adjusting the [`timeouts_sync` parameter](./code_mgr_custom.html#timeoutssync).<!--PE-20860-->  

## r10k with the Rugged provider can develop a bloated cache 
 
If you use the Rugged provider for r10k, repository pruning is not supported. This means that if you use many short-lived branches, over time the local r10k cache can become bloated and take up significant disk space.
 
If you encounter this issue, run `git-gc` periodically on any cached repo that is using a large amount of disk space in the cachedir. Alternately, use the [shellgit provider](https://docs.puppet.com/pe/latest./r10k_custom.html#provider), which automatically garbage collects the repos according to the normal Git CLI rules. <!--RK-234-->

## Code Manager and r10k do not identify the default branch for module repositories  

When you use Code Manager or r10k to deploy modules from a Git source, the default branch of the source repository is always assumed to be 'master'. If the module repository uses a default branch that is *not* 'master', this causes an error. To work around this issue, specify the default branch with the [`ref:`](./cmgmt_puppetfile.html#declare-a-git-repo-as-a-module) key in your Puppetfile. <!--RK-261-->

## Code Manager webhook does not delete branches as expected

If you delete a branch from your control repository, the Code Manager webhook deployment does not immediately delete that environment from the Puppet master's staging or live code directories.

Code Manager will delete the environment when it deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually:

```
puppet code deploy --all --wait
```

## After an error during the initial run of file sync, Puppet Server won't start

The first time you run Code Manager and file sync on a master, an error can occur that prevents Puppet Server from starting. <!--PE-16144--> To work around this issue:

1. Stop the pe-puppetserver service.
2. Locate the data-dir variable in `/etc/puppetlabs/puppetserver/conf.d/file-sync.conf`.
3. Remove the directory.
4. Start the pe-puppetserver service.

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

