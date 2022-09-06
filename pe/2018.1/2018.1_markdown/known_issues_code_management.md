# Code management known issues

These are the known issues for Code Manager, r10k, and file sync in this release.

## **Default SSH URL with TFS fails with Rugged error**

Using the default SSH URL with Microsoft Team Foundation Server \(TFS\) with the `rugged` provider causes an error of "unable to determine current branches for Git source." This is because the `rugged` provider expects an `@` symbol in the URL format.

To work around this error, replace `ssh://` in the default URL with `git@`

For example, change:

```
ssh://tfs.puppet.com:22/tfs/DefaultCollection/Puppet/_git/control-repo
```

to

```
git@tfs.puppet.com:22/tfs/DefaultCollection/Puppet/_git/control-repo
```

## GitHub security updates might cause errors with `shellgit`

GitHub has disabled TLSv1, TLSv1.1 and some SSH cipher suites, which might cause automation using older crypto libraries to start failing. If you are using Code Manager or r10k with the `shellgit` provider enabled, you might see negotiation errors on some platforms when fetching modules from the Forge. To resolve these errors, switch your configuration to use the `rugged` provider, or fix `shellgit` by updating your OS package.

## Timeouts when using `--wait` with large deployments or geographically dispersed compile masters

Because the `--wait` flag deploys code to all compile masters before returning results, some deployments with a large node count or compile masters spread across a large geographic area might experience a timeout. Work around this issue by adjusting the `timeouts_sync` parameter.

## r10k with the Rugged provider can develop a bloated cache

If you use the `rugged` provider for r10k, repository pruning is not supported. As a result, if you use many short-lived branches, over time the local r10k cache can become bloated and take up significant disk space.

If you encounter this issue, run `git-gc` periodically on any cached repo that is using a large amount of disk space in the cachedir. Alternatively, use the `shellgit` provider, which automatically garbage collects the repos according to the normal Git CLI rules.

## Code Manager and r10k do not identify the default branch for module repositories

When you use Code Manager or r10k to deploy modules from a Git source, the default branch of the source repository is always assumed to be master. If the module repository uses a default branch that is *not* master, an error occurs. To work around this issue, specify the default branch with the `ref:` key in your Puppetfile.

## Code Manager webhook does not delete branches as expected

If you delete a branch from your control repository, the Code Manager webhook deployment does not immediately delete that environment from the master's staging or live code directories.

Code Manager deletes the environment when it deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually:

```
puppet code deploy --all --wait
```

## After an error during the initial run of file sync, Puppet Server won't start

The first time you run Code Manager and file sync on a master, an error can occur that prevents Puppet Server from starting. To work around this issue:

1.  Stop the `pe-puppetserver` service.
2.  Locate the data-dir variable in `/etc/puppetlabs/puppetserver/conf.d/file-sync.conf`.
3.  Remove the directory.
4.  Start the `pe-puppetserver` service.

Repeat these steps on each master exhibiting the same symptoms, including the master of masters and any compile masters.

## Puppet Server crashes if file sync can't write to the live code directory

If the live code directory contains content that file sync didn’t expect to find there \(for example, someone has made changes directly to the live code directory\), Puppet Server crashes.

The following error appears in `puppetserver.log`:

```
2016-05-05 11:57:06,042 ERROR [clojure-agent-send-off-pool-0] [p.e.s.f.file-sync-client-core] Fatal error during file sync, requesting shutdown.
org.eclipse.jgit.api.errors.JGitInternalException: Could not delete file /etc/puppetlabs/code/environments/development
        at org.eclipse.jgit.api.CleanCommand.call(CleanCommand.java:138) ~[puppet-server-release.jar:na]
```

To recover from this error:

1.  Delete the environments in code dir: `find /etc/puppetlabs/code -mindepth 1 -delete`
2.  Start the `pe-puppetserver` service: `puppet resource service pe-puppetserver ensure=running`
3.  Trigger a Code Manager run by your usual method.

## Code Manager webhook authentication for GitLab

Prior to GitLab 8.5.0, GitLab had a character limit on webhooks, so the entire Code Manager authentication token could not be passed into GitLab webhooks. If you are using a GitLab version earlier that 8.5.0, either upgrade to the current GitLab version or turn off Code Manager webhook authentication via Hiera with the `authenticate_webhook` parameter.

## File sync error causes `pe-puppetserver` service to shut down due to incorrect `pe-puppet` owner and group permissions

During an r10k deployment, if the `pe-puppet` user does not have permission to delete files in the environment directory, a file sync error causes the `pe-puppetserver` service to shut down during deployment.

This issue can be fixed by setting permissions in the environment directory to owner `pe-puppet` and group `pe-puppet` with this command: `chown -R pe-puppet:pe-puppet /etc/puppetlabs/code/environments`.

