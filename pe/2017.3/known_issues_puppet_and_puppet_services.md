# Puppet and Puppet services known issues

These are the known issues for Puppet and Puppet services in this PE release.

## String translation results in degraded performance

When gettext is installed in order to translate strings and environment caching is not used \(`environment_timeout = 0`\) performance might degrade. As a workaround, you can use a new setting, `puppet_enterprise::master::disable_i18n`, to disable string translation and thereby improve performance. By default, string translation is turned off \(`puppet_enterprise::master::disable_i18n = true`\).

## Restart shell after install for PE client tools subcommands

After installing PE, the commands in the PE client tools aren't available on the PATH until you restart your shell.

## Change to `lsbmajdistrelease` fact affects some manifests

In Facter 2.2.0, the `lsbmajdistrelease` fact changed its value from the first two numbers to the full two-number.two-number version on Ubuntu and Amazon Linux systems. This might break manifests that were based on the previous behavior. For example, this fact changed from: `12` to `12.04`.

## Change `allow_no_actionpolic`y parameter to enforce MCollective action policies

The MCollective ActionPolicy plugin is installed by default in PE. Within the configuration of MCollective, there is a setting that can be used to enforce the use of this ActionPolicy. By default this setting \(`plugin.actionpolicy.allow_unconfigured`\) is hardcoded to `1`. This default prevents you from enforcing the use of configured action policies.

To change this setting, use the console to edit the value of the `allow_no_actionpolicy` parameter of the `puppet_enterprise::profile::mcollective::agent` class located in the **PE MCollective** node group. To allow ActionPolicy, enter `"0"`.

## `puppet module list --tree` shows incorrect dependencies after uninstalling modules

If you uninstall a module with `puppet module uninstall <module name>` and then run `puppet module list --tree`, you see a tree that does not accurately reflect module dependencies.

## The Puppet module tool does not support Solaris 10

When attempting to use the PMT on Solaris 10, you'll get an error like:

```
    Error: Could not connect via HTTPS to https://forgeapi.puppetlabs.com
            Unable to verify the SSL certificate
    The certificate may not be signed by a valid CA
    The CA bundle included with OpenSSL may not be valid or up to date
```

This error occurs because there is no CA-cert bundle on Solaris 10 to trust the Forge certificate. To work around this issue, download directly from the Forge website and then use the PMT to install from a local tarball.

