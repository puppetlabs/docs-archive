---
layout: default
title: "Puppet Plugin for VMware vRealize Orchestrator Release Notes"
canonical: "/pe/latest/vro_release_notes.html"
description: "Release notes for the Puppet plug-in for VMware vRealize Orchestrator (vRO)."
---

See the [vRO Puppet plug-in documentation](./vro_intro.html) for more information about the plug-in and instructions on installing and using it.

## vRO Puppet plug-in 3.0

Released May 16, 2017.

* Adds support for integration within the vRealize Automation Enterprise GUI in version 7.3 and higher.
* Role classes can be dynamically fetched from the Puppet master.
* vRA GUI displays each role class’s description based on the `@summary` puppet-strings tag if it is present.
* Major version release that drops support for open source Puppet.
* One vRA/vRO instance can support multiple Puppet masters.
* `csr_attributes.yaml` file is deleted after provisioning if you are using the shared-secret autosigning workflow.
* If Puppet agent runs during provision do not succeed, the Puppet agent service is left in running state.
* Adjustments to avoid timeouts in Windows provisioning.
* `Puppet.SSH.useSudo` is now exposed as a custom property.
* Improved error handling in the "Add a Puppet Master" workflow.
* Improved error message if `Puppet:Master` is not set.
* "Run Puppet" workflows no longer perform one more Puppet run than necessary.

## vRO Puppet plug-in 2.0

This all-new plug-in release adds support for Puppet 4 features, more robust support for vRO and vRA, and new content for Puppet administrators and vRO blueprint developers alike.

## Known issues

#### Known issue: Windows 32-bit agents are not supported

The vRO Puppet plug-in can't manage agents running the 32-bit (x86) version of the Puppet agent. Only 64-bit (x64) agents are supported.

> **Upgrade warning:** The plug-in **does not** support upgrades from the previous vRO Puppet plug-in versions (i.e. 1.0, 2.0). If you're using any previous version of the plug-in, you must [completely remove it](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2064575) before installing. The vRO starter content repository includes [a script to assist with removing the plug-in](https://github.com/puppetlabs/puppet-vro-starter_content/blob/production/scripts/remove_plugin.sh).
