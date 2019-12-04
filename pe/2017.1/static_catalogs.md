---
layout: default
title: "Static Catalogs in Puppet Enterprise"
canonical: "/pe/latest/static_catalogs.html"
---

> #### What's a catalog?
>
> A catalog is a document that describes the [desired state for each resource]({{puppet}}/lang_resources.html) that Puppet manages on a node. A Puppet master typically compiles a catalog from manifests of Puppet code. For more information about catalogs and compilation, see [our step-by-step overview of the catalog compilation process]({{puppet}}/subsystem_catalog_compilation.html).

## What's a static catalog?

A *static* catalog includes metadata that specifies the desired state of any [file resources]({{puppet}}/types/file.html) on a node that have `source` attributes pointing to `puppet:///` locations. This metadata can refer to a specific version of the file, rather than the latest version, and can confirm that the agent is applying the appropriate version of the file resource for the catalog. Also, because the metadata is provided in the catalog, Puppet agents make fewer requests to the master.

## Why use static catalogs?

When a Puppet master produces a non-static catalog, the catalog doesn't specify the version of file resources. When the agent applies the catalog, it always retrieves the latest version of that file resource, or uses a previously retrieved version if it matches the latest version's contents. Note that this potential problem affects file resources that use the [`source` attribute]({{puppet}}/types/file.html#file-attribute-source). File resources that use the [`content` attribute]({{puppet}}/types/file.html#file-attribute-content) are not affected, and their behavior will not change in static catalogs.

When a Puppet manifest depends on a file whose contents change more frequently than the Puppet agent receives new catalogs, a node might apply a version of the referenced file that doesn't match the instructions in the catalog. In PE, such situations are particularly likely if you’ve [configured your agents to run off cached catalogs](./orchestrator_install.html#prepare-your-agents-for-orchestration-services) for participation in application orchestration services.

Consequently, the agent's Puppet runs might produce different results each time the agent applies the same catalog. This often causes problems because Puppet generally expects a catalog to produce the same results each time it's applied, regardless of any code or file content updates on the master.

Additionally, each time a Puppet agent applies a normal cached catalog that contains file resources sourced from `puppet:///` locations, the agent requests [file metadata]({{puppet}}/http_api/http_file_metadata.html) from the Puppet master each time the catalog's applied, even though nothing's changed in the cached catalog. This causes the master to perform unnecessary resource-intensive checksum calculations for each file resource.

Static catalogs avoid these problems by including metadata that refers to a specific version of the resource's file. This prevents a newer version from being incorrectly applied, and avoids having the agent regenerate the metadata on each Puppet run. The metadata is delivered in the form of a unique hash maintained, by default, by PE's [file sync service](./cmgmt_filesync.html).

We call this type of catalog "static" because it contains all of the information that an agent needs to determine whether the node's configuration matches the instructions and state of file resources **at the static point in time when the catalog was generated.**

### Summarizing the differences in catalog behavior

**Without static catalogs enabled:**

1. The Puppet agent sends facts to the Puppet master and requests a catalog.
2. The Puppet master compiles and returns the agent's catalog.
3. The Puppet agent applies the catalog by checking each resource the catalog describes. If it finds any resources that are not in the desired state, it makes the necessary changes.

**With static catalogs enabled:**

1. The Puppet agent sends facts to the Puppet master and requests a catalog.
2. The Puppet master compiles and returns the agent's catalog, including metadata that that specifies the desired state of the node's file resources.
3. The Puppet agent applies the catalog by checking each resource the catalog describes. If the agent finds any resources that are not in the desired state, it makes the necessary changes based on the state of the file resources at the static point in time when the catalog was generated.
4. If you change code on the Puppet master, file contents are not updated until the agent requests a new catalog with new file metadata.

## Enabling static catalogs

In PE, static catalogs are disabled across all environments for new installations. To use static catalogs in PE, **you must** [enable file sync](./cmgmt_filesync.html#enabling-or-disabling-file-sync).  Once file sync is enabled, Puppet Server automatically creates static catalogs containing file metadata for eligible resources, and Puppet agents running Puppet 1.4.0 or newer can take advantage of the catalogs' new features.

If you do not enable file sync and code manager, you can still use static catalogs, but you will need to create some custom scripts and set a few parameters in the PE console. See [Using static catalogs without file sync](#using-static-catalogs-without-file-sync-service).

## Disabling static catalogs globally with Hiera

To disable static catalogs globally, you can [edit your Hiera default .yaml file](./config_intro.html#configure-settings-with-hiera) and apply the following setting:

`puppet_enterprise::master::static_catalogs: false`

## Using static catalogs without file sync

To use static catalogs without enabling file sync, you must set the `code_id` and `code_content` aparameters in Puppet, and then configure the `code_id_command`, `code_content_command`, and `file_sync_enabled` parameters in the PE console.

**To use static catalogs without file sync:**

1. Set `code_id` and `code_content` by following the instructions in the open source Puppet [static catalogs documentation]({{puppet}}/static_catalogs.md#configuring-code_id-and-code_content). Then return to this page to set the remaining parameters.
2. In the PE console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** node group.
3. On the **Classes** tab, locate the `puppet_enterprise::profile::master` class, and select **file_sync_enabled** from its **Parameter** list.
4. In the **Value** field, enter *false*, and click **Add parameter**.
5. Select the **code_id_command** parameter, and in the **Value** field, enter the absolute path to the `code_id` script, and click **Add parameter**.
6. Select the **code_content_command** parameter, and in the **Value** field, add the absolute to the `code_content` script, and click **Add parameter**
7. Commit changes.
8. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the Puppet master.

## Enforcing change with new catalogs

When you are ready to deploy new Puppet code and deliver new static catalogs, you don't need to wait for Puppet agents to check in. Use the [Puppet orchestrator](./orchestrator_intro.html) to enforce change and deliver new catalogs across your PE infrastructure, on a per-environment basis.

## When aren't static catalogs applied?

In the following scenarios, either agents won't apply static catalogs, or Puppet catalogs won't include metadata for file resources.

* Static catalogs are globally disabled.
* Code Manager and file sync are disabled, and `code_id` and `code_content` aren't configured.
* Your Puppet agents aren't running 2016.1 or later (Puppet agent version 1.4.0 or later).

Additionally, Puppet won't include metadata for a file resource if it:

* Uses the `content` attribute instead of the `source` attribute.
* Uses the `source` attribute with a non-Puppet scheme (for example `source => 'http://host:port/path/to/file'`).
* Uses the `source` attribute without the built-in modules mount point.
* Uses the `source` attribute, but the file on the Puppet master is not in `/etc/puppetlabs/code/environments/<environment>/*/*/files/**`. For example, module files are typically in `/etc/puppetlabs/code/environments/<environment>/modules/<module_name>/files/**`.

Puppet agents will continue to process the catalogs in these scenarios, but without the benefits of inlined file metadata or file resource versions.