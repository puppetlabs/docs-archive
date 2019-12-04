# Known issues

These are the known issues in PE 2019.0.

**Tip:** We cleaned up known issues in this version of the PE docs, removing obsolete issues and incorporating workarounds for issues we can't or won't fix into relevant sections of the docs. 

## Installation and upgrade known issues

These are the known issues for installation and upgrade in this release.

### Upgrade can fail in environments with external PE-PostgreSQL

When you run the installer in an environment with an external PE-PostgreSQL database, if the orchestrator database user name is not `pe-orchestrator`, upgrade fails on the master and can result in downtime.

### Console output is shown when installing in quiet mode

When running the installer in text mode, adding the `-q` option does not successfully activate quiet mode, and the installation process is logged in the console.

### PE upgrade to 2019.0.2 crashes for Continuous Delivery for Puppet Enterprise users with impact analysis enabled

If you installed Continuous Delivery for Puppet Enterprise \(PE\) and enabled impact analysis using the puppetlabs-cd4pe module version 1.0.3 or earlier, PE crashes with a `Unit pe-puppetserver.service has failed.` error when upgraded to version 2019.0.2. To avoid this issue, upgrade the [puppetlabs-cd4pe](https://forge.puppet.com/puppetlabs/cd4pe) module to version 1.0.4 before upgrading PE to 2019.0.2. To fix the error after upgrading to 2019.0.2, disable impact analysis and upgrade the module to version 1.0.4.

### Web-based installation requires a second Puppet run to fully install PE services

Web-based installation includes a single, initial Puppet run, but a second Puppet run is required in order to populate `services.conf`. Until a second Puppet run completes, the Puppet service status in the console reports that one or more services isn't accepting requests.

### **Installer can fail due to SSL errors with AmazonAWS**

In some cases when attempting to install PE, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and thus have been unable retrieve puppet-agent packages needed for installation. In most cases, you should be able to properly install after updating the CA cert bundle on the master platform. To update the bundle, run the following commands:

```
rm /etc/ssl/certs/ca-bundle.crt
yum reinstall ca-certificates
```

After updating the CA bundle, run the PE installer again.

## Orchestration services known issues

These are the known issues for the orchestration services in this release.

### Orchestrator fails when rerunning tasks on agentless nodes

When you rerun tasks from the Job details page, target nodes that do not have the Puppet agent installed are miscategorized as PuppetDB nodes. This causes the orchestrator to fail on those nodes.

## Permissions known issues

These are the known issues for user permissions and user roles in this release.

## Code management known issues

These are the known issues for Code Manager, r10k, and file sync in this release.

### Default SSH URL with TFS fails with rugged error

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

### GitHub security updates might cause errors with `shellgit`

GitHub has disabled TLSv1, TLSv1.1 and some SSH cipher suites, which might cause automation using older crypto libraries to start failing. If you are using Code Manager or r10k with the `shellgit` provider enabled, you might see negotiation errors on some platforms when fetching modules from the Forge. To resolve these errors, switch your configuration to use the `rugged` provider, or fix `shellgit` by updating your OS package.

### Timeouts when using `--wait` with large deployments or geographically dispersed compile masters

Because the `--wait` flag now deploys code to all compile masters before returning results, some deployments with a large node count or compile masters spread across a large geographic area might experience a timeout. Work around this issue by adjusting the `timeouts_sync` parameter.

### r10k with the Rugged provider can develop a bloated cache

If you use the Rugged provider for r10k, repository pruning is not supported. As a result, if you use many short-lived branches, over time the local r10k cache can become bloated and take up significant disk space.

If you encounter this issue, run `git-gc` periodically on any cached repo that is using a large amount of disk space in the cachedir. Alternately, use the shellgit provider, which automatically garbage collects the repos according to the normal Git CLI rules.

### Code Manager and r10k do not identify the default branch for module repositories

When you use Code Manager or r10k to deploy modules from a Git source, the default branch of the source repository is always assumed to be master. If the module repository uses a default branch that is *not* master, an error occurs. To work around this issue, specify the default branch with the `ref:` key in your Puppetfile.

### After an error during the initial run of file sync, Puppet Server won't start

The first time you run Code Manager and file sync on a master, an error can occur that prevents Puppet Server from starting. To work around this issue:

1.  Stop the `pe-puppetserver` service.
2.  Locate the data-dir variable in `/etc/puppetlabs/puppetserver/conf.d/file-sync.conf`.
3.  Remove the directory.
4.  Start the `pe-puppetserver` service.

Repeat these steps on each master exhibiting the same symptoms, including the master of masters and any compile masters.

### Puppet Server crashes if file sync can't write to the live code directory

If the live code directory contains content that file sync didn’t expect to find there \(for example, someone has made changes directly to the live code directory\), Puppet Server crashes.

The following error appears in `puppetserver.log`:

```
2016-05-05 11:57:06,042 ERROR [clojure-agent-send-off-pool-0] [p.e.s.f.file-sync-client-core] Fatal error during file sync, requesting shutdown.
org.eclipse.jgit.api.errors.JGitInternalException: Could not delete file /etc/puppetlabs/code/environments/development
        at org.eclipse.jgit.api.CleanCommand.call(CleanCommand.java:138) ~[puppet-server-release.jar:na]
```

To recover from this error:

1.  Delete the environments in code dir: `find /etc/puppetlabs/code -mindepth 1 -delete`.
2.  Start the `pe-puppetserver` service: `puppet resource service pe-puppetserver ensure=running`
3.  Trigger a Code Manager run by your usual method.

## Backup and restore known issues

These are the known issues for backup and restore in this release.

### Restoring the `pe-rbac` database fails with the `puppet-backup restore` command

When restoring the `pe-rbac` database, the restore process exits with errors about a duplicate operator family, `citext_ops`.

To work around this issue: 

1.  Log into your existing PostgreSQL instance: 

```
sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql pe-rbac"
```

2.  Issue these commands: 

```
ALTER EXTENSION citext ADD operator family citext_ops using btree; 
ALTER EXTENSION citext ADD operator family citext_ops using hash
```

3.  Exit the PostgreSQL shell and re-run the backup utility.


### `puppet-backup` fails if gems are missing from the master's agent environment

The `puppet-backup` create command might fail if any gem installed on the Puppet Server isn't present on the agent environment on the master. If the gem is either absent or of a different version on the master's agent environment, you get the error "command `puppet infrastructure recover_configuration` failed".

To fix this, you must install any missing or incorrectly versioned gems on the master's agent environment. To find which gems are causing the error, check the backup logs for any gem incompatibility issues with the error message. PE creates backup logs as a `report.txt` whenever you run a `puppet-backup` command.

To see what gems and their versions you have installed on your Puppet Server, run the command `puppetserver gem list` . To see what gems are installed in the agent environment on your master, run `/opt/puppetlabs/puppet/bin/gem list`.

## Internationalization known issues

These are the known issues for internationalization and UTF-8 support in this release.

### ASCII limitations

Certain elements of Puppet and PE are limited to ASCII characters only, or work best with ASCII characters, including:

-   Names for environments, variables, classes, resource types, modules, parameters, and tags in the Puppet language.

-   File names, which can generate errors when referenced as a `source` in a file resource or concat fragment.

-   The `title` and `namevar` for certain resource types, on certain operating systems. For example, the user and group resources on RHEL and CentOS can contain only ASCII characters in `title` and `namevar`.

-   The console password.


### Ruby can corrupt the `path` fact and environment variable on Windows

There is a bug in Ruby that can corrupt the environment variable names and values. This bug causes corruption for only some codepages. This bug might be triggered when environment names or values contain UTF-8 characters that can't be translated to the current codepage.

The same bug can cause the `path` fact to be cached in a corrupt state.

