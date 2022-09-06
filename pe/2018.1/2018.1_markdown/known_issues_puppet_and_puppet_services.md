# Puppet and Puppet services known issues

These are the known issues for Puppet and Puppet services in this release.

## Restart shell after install for PE client tools subcommands

After installing PE, the commands in the PE client tools aren't available on the PATH until you restart your shell.

## Change to `lsbmajdistrelease` fact affects some manifests

In Facter 2.2.0, the `lsbmajdistrelease` fact changed its value from the first two numbers to the full two-number.two-number version on Ubuntu and Amazon Linux systems. This might break manifests that were based on the previous behavior. For example, this fact changed from: `12` to `12.04`.

## Change `allow_no_actionpolicy` parameter to enforce MCollective action policies

The MCollective ActionPolicy plugin is installed by default in PE. Within the configuration of MCollective, there is a setting that can be used to enforce the use of this ActionPolicy. By default this setting \(`plugin.actionpolicy.allow_unconfigured`\) is hardcoded to `1`. This default prevents you from enforcing the use of configured action policies.

To change this setting, use the console to edit the value of the `allow_no_actionpolicy` parameter of the `puppet_enterprise::profile::mcollective::agent` class located in the **PE MCollective** node group. To allow ActionPolicy, enter `"0"`.

## `puppet module list --tree` shows incorrect dependencies after uninstalling modules

If you uninstall a module with `puppet module uninstall <module name>` and then run `puppet module list --tree`, you see a tree that does not accurately reflect module dependencies.

## The `puppet module` command does not support Solaris 10

When attempting to use the `puppet module` command on Solaris 10, you'll get an error like:

```
    Error: Could not connect via HTTPS to https://forgeapi.puppetlabs.com
            Unable to verify the SSL certificate
    The certificate might not be signed by a valid CA
    The CA bundle included with OpenSSL might not be valid or up to date
```

This error occurs because there is no CA-cert bundle on Solaris 10 to trust the Forge certificate. To work around this issue, download directly from the Forge website and then use the `puppet module install` command to install from a local tarball.

## `hiera-eyaml` gem does not include an executable

The `hiera-eyaml` gem was included with Puppet agent 5.5.0, but is not installed to a location in the working environment's PATH until Puppet agent 5.5.7, which can lead to unexpected Puppet agent failures:

```
Error: Could not find command 'eyaml'
Error: /Stage[main]/Hiera::Eyaml/Exec[createkeys]/returns: change from 'notrun' to ['0'] failed: Could not find command 'eyaml
```

The preferred solution is to update Puppet to 5.5.7 or newer. To work around this issue on Puppet agent 5.5.0 to 5.5.6, link the installed `hiera-eyaml` gem executable to that path:

```
ln -s /opt/puppetlabs/puppet/lib/ruby/vendor_gems/bin/eyaml /opt/puppetlabs/puppet/bin/eyaml
```



## Puppet stdlib functions handle `undef` too leniently

PE 2018.1 uses Puppet 5 stdlib functions. These functions handle `undef` less strictly then they should. In Puppet 6, many functions from stdlib were moved into Forge modules, and now treat `undef` more strictly. Consequently, in PE 2019, some code that relies on `undef`values being implicitly treated as other types will return an evaluation error. For more information on which functions were moved out of stdlib, see the [Puppet 6.0 release notes](https://puppet.com/docs/puppet/6.0/release_notes_puppet.html#select-types-moved-to-modules).

