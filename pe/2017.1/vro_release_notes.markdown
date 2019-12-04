---
layout: default
title: "Puppet Plugin for VMware vRealize Orchestrator Release Notes"
canonical: "/pe/latest/vro_release_notes.html"
description: "Release notes for the Puppet plug-in for VMware vRealize Orchestrator (vRO)."
---

See the [vRO Puppet plug-in documentation](./vro_intro.html) for more information about the plug-in and instructions on installing and using it.

## vRO Puppet plug-in 2.0

This all-new plug-in release adds support for Puppet 4 features, more robust support for vRO and vRA, and new content for Puppet administrators and vRO blueprint developers alike.

> **Upgrade warning:** This version of the plug-in **does not** support upgrades from the previous vRO Puppet plug-in version (1.0). If you're using version 1.0 of the plug-in, you must [completely remove it](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2064575) before installing version 2.0. The vRO starter content repository includes [a script to remove version 1.0 of the  plugin](https://github.com/puppetlabs/puppet-vro-starter_content/blob/production/scripts/remove_plugin.sh).

### Known issue: Windows 32-bit agents are not supported

The vRO Puppet plug-in can't manage agents running the 32-bit (x86) version of the Puppet agent. Only 64-bit (x64) agents are supported by this release.

### Known issue: "Run Puppet" workflows perform one more Puppet run than necessary

The "Run Puppet" workflows on both Linux and Windows agents include a restart of the puppet service, via the `puppet resource service puppet` command.

For each successful "Run Puppet" workflow run, there appears two seemingly identical reports in the PE Console UI due to a second consecutive Puppet run.

This redundant report can be safely disregarded, and we plan to address this behavior in future versions.

### Known issue: Able to attempt workflows even when no Puppet master is defined

In the sample Event Broker workflows, you can start a workflow even if the Puppet master is not defined, resulting in several actions occurring before it fails.

In a future version, the plug-in will stop sooner when attempting to run workflows that require a Puppet master if one isn't defined.
