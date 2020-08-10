# PE known issues

These are the known issues in PE 2019.7.

## Installation and upgrade known issues

These are the known issues for installation and upgrade in this release.

### Upgrade removes custom classification rules from **PE Master** node group

Custom rules that you've used to classify compilers in the **PE Master** node group are removed upon upgrade, or when you run `puppet infrastructure configure`. This issue is due to a change in the way compilers are classified in 2019.7 and later, with new-style compilers classified in the new **PE Compiler** node group.

As a workaround, before upgrade, pin compilers to the **PE Master** node group. After upgrade, you can optionally unpin compiler nodes from the **PE Master** node group and reimplement any custom rules.

**Important:** If you reimplement rules in order to classify compilers, leave the new `pp_auth_role` rule in place. This rule enables converting legacy compilers when you're ready to do so.

### Upgrading agents using the puppet\_agent module can produce non-breaking errors

Upgrading agents from versions 6.14 or 6.15 using the puppet-agent module can produce errors about an unavailable file resource or unknown HTTP resource. These errors occur only during the initial Puppet agent run, when the agent is still using versions 6.14 or 6.15 with an updated master. The error resolves after the `puppet-agent` service restarts.

### Converting legacy compilers fails with autosigning enabled

Running `puppet infrastructure run convert_legacy_compiler` with autosigning enabled causes the conversion to fail during certificate regeneration. As a workaround, disable autosigning before converting compilers, or follow these steps to complete a failed conversion:

1.  On affected compilers, run Puppet: `puppet agent -t`
2.  Unpin compiler nodes from **PE Master** group, either in the console or from the command line:

    ```
    /opt/puppetlabs/bin/puppet resource pe_node_group "PE Master" unpinned="COMPILER_FQDN"
    ```

3.  On your master, in `pe.conf`, remove the entry `puppet_enterprise::profile::database::private_temp_puppetdb_host`
4.  If you have an external PE-PostgreSQL node, run Puppet on it: `puppet agent -t`
5.  On your master, run Puppet: `puppet agent -t`
6.  On affected compilers, run Puppet: `puppet agent -t`

### Command to remove old PostgreSQL versions fails on Ubuntu

When run on Ubuntu nodes, the `puppet infrastructure run remove_old_postgresql_versions` command fails, erroneously reporting that PostgreSQL isn't installed.

### Upgrade fails with a Could not retrieve facts error

If an invalid custom fact installed on the master can't be resolved during upgrade, the upgrade fails with an error similar to this:

```
Could not retrieve facts ... undefined method `split' for nil:NilClass (Puppet::Error)from /opt/puppetlabs/installer/lib/ruby/gems/2.5.0/gems/facter-4.0.20/lib/custom_facts/util/loader.rb:125:in `load'
```

To work around this issue, fix any invalid custom facts at the path indicated in the error and then reattempt the upgrade.

### Legacy compiler conversion fails with an external certificate authority

If you use an external certificate authority \(CA\), the `puppet infrastructure run convert_legacy_compiler` command fails with an error during the certificate-signing step.

```
Agent_cert_regen: ERROR: Failed to regenerate agent certificate on node <compiler-node.domain.com>
Agent_cert_regen: bolt/run-failure:Plan aborted: run_task 'enterprise_tasks::sign' failed on 1 target
Agent_cert_regen: puppetlabs.sign/sign-cert-failed Could not sign request for host with certname <compiler-node.domain.com> using caserver <master-host.domain.com>
```

To work around this issue when it appears:

1.  Log on to the CA server and manually sign certificates for the compiler.
2.  On the compiler, run Puppet: `puppet agent -t`
3.  Unpin the compiler from **PE Master** group, either from the console, or from the CLI using the command: `/opt/puppetlabs/bin/puppet resource pe_node_group "PE Master" unpinned="<COMPILER_FQDN>"`
4.  On your master, in the `pe.conf` file, remove the entry `puppet_enterprise::profile::database::private_temp_puppetdb_host`
5.  If you have an external PE-PostgreSQL node, run Puppet on that node: `puppet agent -t`
6.  Run Puppet on your master: `puppet agent -t`
7.  Run Puppet on all compilers: `puppet agent -t`

### Upgrading a replica can temporarily lock the agent on the master

If you try to run Puppet on your master before the `puppet infrastructure upgrade replica` command has completed, you can encounter an error that a Puppet run is already in progress. To resolve the issue, run Puppet on the master: `puppet agent -t`.

### Missing package dependencies for SUSE Linux Enterprise Server agent nodes

On agent nodes running SUSE Linux Enterprise Server 15, the `libyaml-cpp` package and operating system packages prefixed with `libboost_` are no longer bundled with Puppet agent, and might not be included in the operating system. See [SUSE Linux Enterprise Server dependencies](supported_operating_systems.md#) for the full list of required packages. The `libyaml-cpp` package is in the Desktop Applications SUSE Linux Enterprise Server packaging module, and `libboost` packages are in the Basesystem module. If you encounter issues when installing Puppet agent on SUSE Linux Enterprise Server 15 nodes, use `zypper package-search` or the SUSE Linux Enterprise Server Customer Center packages list at [https://scc.suse.com/packages](https://scc.suse.com/packages) to locate the packages and instructions on how to install them manually.

### Installer can fail due to SSL errors with AmazonAWS

In some cases when attempting to install PE, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and thus have been unable retrieve puppet-agent packages needed for installation. In most cases, you should be able to properly install after updating the CA cert bundle on the master platform. To update the bundle, run the following commands:

```
rm /etc/ssl/certs/ca-bundle.crt
yum reinstall ca-certificates
```

After you update the CA bundle, run the installer again.

### Upgrades can fail when using custom database certificate parameters

When upgrading an infrastructure with an unmanaged PostgreSQL database to this version of PE, the upgrade can fail and result in downtime if the databases use custom certificates not issued by the PE certificate authority. This is the case even if you configured the `database_properties` parameters in `pe.conf` to set a custom `sslrootcert`. The upgrader ignores these custom certificate settings.

To manually override the upgrader's certificate settings, run these commands, replacing <NEW CERT\> with the name of your custom certificate.

```
rpm -iUv /opt/puppetlabs/server/data/packages/public/2019.3.0/el-7-x86_64-6.12.0/pe-modules-2019.3.0.2-1.el7.x86_64.rpm
                    
sed -i 's#/etc/puppetlabs/puppet/ssl/certs/ca.pem#/etc/puppetlabs/puppet/ssl/<NEW CERT>.pem#'
/opt/puppetlabs/server/data/environments/enterprise/modules/pe_manager/lib/puppet_x/puppetlabs/meep/configure/postgres.rb
                        
./puppet-enterprise-installer - --force
```

**Note:** The first step in the code block above includes paths specific to the PE version you're upgrading to. Replace these version information as needed.

## High availability known issues

These are the known issues for high availability in this release.

### Enabling a replica can fail immediately after provisioning

When running `puppet infrastructure provision replica --enable`, the command can fail after the replica is provisioned but before it is enabled if services on the replica are still starting up. As a workaround, if you get an error that the replica can't be enabled, verify that the replica is available with `puppet infrastructure status --verbose`, and then enable the replica separately: `puppet infrastructure enable replica <REPLICA NODE NAME>`.

### Upgrading a replica can temporarily lock the agent on the master

If you try to run Puppet on your master before the `puppet infrastructure upgrade replica` command has completed, you can encounter an error that a Puppet run is already in progress. To resolve the issue, run Puppet on the master: `puppet agent -t`.

### Puppet runs can take longer than expected in failover scenarios

In an HA environment with a provisioned replica, if the master is unreachable, a Puppet run using data from the replica can take up to three times as long as expected \(for example, 6 minutes versus 2 minutes\).

## FIPS known issues

These are the known issues with supported platforms in this release.

### Puppet Server FIPS installations don’t support Ruby’s OpenSSL module

FIPS-compliant installations don't support extensions that use the standard Ruby Open SSL library, such as heira-eyaml.

### FIPS installations don't support Razor

Razor, which is deprecated, doesn't work and isn't supported in FIPS-enabled PE.

### Errors when using `puppet code` and `puppet db` commands on FIPS-enabled hardware

When the `pe-client-tools` packages are run on FIPS-enabled hardware, `puppet code` and `puppet db` commands fail with SSL handshake errors. To use `puppet db` commands on a FIPS-enabled machine, install the [puppetdb\_cli](https://rubygems.org/gems/puppetdb_cli) Ruby gem with the following command:

```
/opt/puppetlabs/puppet/bin/gem install puppetdb_cli --bindir /opt/puppetlabs/bin/
```

To use `puppet code` commands on a FIPS-enabled machine, use the Code Manager API. Alternatively, you can use `pe-client-tools` on a non-FIPS-enabled machine to access a FIPS-enabled master.

## Supported platforms known issues

These are the known issues with supported platforms in this release.

### Ubuntu 20.04 can't be installed with PE package management

Ubuntu 20.04 is a supported agent platform, but isn't available for installation as a `pe_repo` class. As a workaround, on your master,add `/opt/puppetlabs/puppet/modules/pe_repo/manifests/platform/ubuntu_2004_amd64.pp` with this code:

```
class pe_repo::platform::ubuntu_2004_amd64(
  $agent_version = $::aio_agent_build,
){
  include pe_repo
 
  pe_repo::debian { 'ubuntu-20.04-amd64':
    agent_version => $agent_version,
    codename      => 'focal',
    pe_version    => $pe_repo::default_pe_build,
  }
}
```

You can then add Ubuntu 20.04 as a `pe_repo` class and install the PE agent on Ubuntu 20.04 nodes.

## Configuration and maintenance known issues

These are the known issues for configuration and maintenance in this release.

### Console is inaccessible with PE set to IPv6

If you specify IPv6, PE Java services still listens to the IPv4 localhost. This mismatch can prevent access to the console as Nginx proxies traffic to the wrong localhost.

As a workaround, set the [java.net.preferIPv6Addresses property](https://docs.oracle.com/javase/8/docs/technotes/guides/net/properties.html) to `true` for console services and PuppetDB:

```
"puppet_enterprise::profile::console::java_args": {
  "Xms": "256m"
  "Xmx": "256m"
  "Djava.net.preferIPv6Addresses": "=true"
}
 
"puppet_enterprise::profile::puppetdb::java_args": {
  "Xms": "256m"
  "Xmx": "256m"
  "Djava.net.preferIPv6Addresses": "=true"
}
```

### `puppet infrastructure recover_configuration` misreports success if specified environment doesn't exist

If you specify an invalid environment when running `puppet infrastructure recover_configuration`, the system erroneously reports that the environment's configuration was saved.

### `puppet-backup` fails if gems are missing from the master's agent environment

The `puppet-backup` create command might fail if any gem installed on the Puppet Server isn't present on the agent environment on the master. If the gem is either absent or of a different version on the master's agent environment, you get the error "command `puppet infrastructure recover_configuration` failed".

To fix this, install missing or incorrectly versioned gems on the master's agent environment. To find which gems are causing the error, check the backup logs for gem incompatibility issues with the error message. PE creates backup logs as a `report.txt` whenever you run a `puppet-backup` command.

To see what gems and their versions you have installed on your Puppet Server, run the command `puppetserver gem list` . To see what gems are installed in the agent environment on your master, run `/opt/puppetlabs/puppet/bin/gem list`.

## Orchestration services known issues

These are the known issues for the orchestration services in this release.

### Orchestrator fails when rerunning tasks on agentless nodes

When you rerun tasks from the **Job details** page, target nodes that do not have an agent installed are miscategorized as PuppetDB nodes. This causes the orchestrator to fail on those nodes.

### Some Bolt stdlib functions fail in PE

The following Bolt stdlib functions return an error if they are run in PE:

-   `apply_prep`
-   `file::read`
-   `file::write`
-   `file::readable`
-   `file::exists`
-   `set_features`
-   `add_to_group`

For more information, see [Plan caveats and limitations](plans_limitations.md).

### The function `puppet plan show` displays invalid plans when Code Manager is disabled

If Code Manager is disabled, the `puppet plan show` function and the console will still list plans that are contained in a control repo, but those plans cannot be run. Enable Code Manager to run plans.

## SSL and certificate known issues

These are the known issues for SSL and certificates in this release.

### Regenerating agent certificates fails with autosign enabled

The `puppet infrastructure run regenerate_agent_certificate` command includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate can't be found, and the command errors out. As a workaround, temporarily disable autosign before running `puppet infrastructure run regenerate_agent_certificate`.

## Code management known issues

These are the known issues for Code Manager, r10k, and file sync in this release.

### Default SSH URL with TFS fails with Rugged error

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

GitHub has disabled TLSv1, TLSv1.1 and some SSH cipher suites, which can cause automation using older crypto libraries to start failing. If you are using Code Manager or r10k with the `shellgit` provider enabled, you might see negotiation errors on some platforms when fetching modules from the Forge. To resolve these errors, switch your configuration to use the `rugged` provider, or fix `shellgit` by updating your OS package.

### Timeouts when using `--wait` with large deployments or geographically dispersed compilers

Because the `--wait` flag deploys code to all compilers before returning results, some deployments with a large node count or compilers spread across a large geographic area might experience a timeout. Work around this issue by adjusting the `timeouts_sync` parameter.

### r10k with the Rugged provider can develop a bloated cache

If you use the `rugged` provider for r10k, repository pruning is not supported. As a result, if you use many short-lived branches, over time the local r10k cache can become bloated and take up significant disk space.

If you encounter this issue, run `git-gc` periodically on any cached repo that is using a large amount of disk space in the cachedir. Alternately, use the `shellgit` provider, which automatically garbage collects the repos according to the normal Git CLI rules.

### Code Manager and r10k do not identify the default branch for module repositories

When you use Code Manager or r10k to deploy modules from a Git source, the default branch of the source repository is always assumed to be master. If the module repository uses a default branch that is *not* master, an error occurs. To work around this issue, specify the default branch with the `ref:` key in your Puppetfile.

### After an error during the initial run of file sync, Puppet Server won't start

The first time you run Code Manager and file sync on a master, an error can occur that prevents Puppet Server from starting. To work around this issue:

1.  Stop the `pe-puppetserver` service.
2.  Locate the data-dir variable in `/etc/puppetlabs/puppetserver/conf.d/file-sync.conf`.
3.  Remove the directory.
4.  Start the `pe-puppetserver` service.

Repeat these steps on each master exhibiting the same symptoms, including any compilers.

### Puppet Server crashes if file sync can't write to the live code directory

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

## Internationalization known issues

These are the known issues for internationalization and UTF-8 support in this release.

### ASCII limitations

Certain elements of Puppet and PE are limited to ASCII characters only, or work best with ASCII characters, including:

-   Names for environments, variables, classes, resource types, modules, parameters, and tags in the Puppet language.

-   File names, which can generate errors when referenced as a `source` in a file resource or concat fragment.

-   The `title` and `namevar` for certain resource types, on certain operating systems. For example, the user and group resources on RHEL and CentOS might contain only ASCII characters in `title` and `namevar`.

-   The console password.


### Ruby can corrupt the `path` fact and environment variable on Windows

There is a bug in Ruby that can corrupt the environment variable names and values. This bug causes corruption for only some codepages. This bug might be triggered when environment names or values contain UTF-8 characters that can't be translated to the current codepage.

The same bug can cause the `path` fact to be cached in a corrupt state.

