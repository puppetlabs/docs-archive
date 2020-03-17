# Static catalogs in Puppet Enterprise

A catalog is a document that describes the desired state for each resource that Puppet manages on a node. A master typically compiles a catalog from manifests of Puppet code. A static catalog is a specific type of Puppet catalog that includes metadata that specifies the desired state of any file resources containing `source` attributes pointing to `puppet:///` locations on a node.

The metadata in a static catalog can refer to a specific version of the file \(not just the latest version\), and can confirm that the agent is applying the appropriate version of the file resource for the catalog. Because the metadata is provided in the catalog, agents make fewer requests to the master.

See the open source Puppet documentation more information about [Resources](https://puppet.com/docs/puppet/latest/lang_resources.html), [File types](https://docs.puppet.com/puppet/latest/type.html#file), and [Catalog compilation](https://docs.puppet.com/puppet/latest/subsystem_catalog_compilation.html).

**Parent topic:**[Configuring Puppet Enterprise](configuring_pe.md)

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

## Enabling static catalogs

When a master produces a non-static catalog, the catalog doesn't specify the version of file resources. When the agent applies the catalog, it always retrieves the latest version of that file resource, or uses a previously retrieved version if it matches the latest version's contents.

This potential problem affects file resources that use the `source` attribute. File resources that use the `content` attribute are not affected, and their behavior does not change in static catalogs.

When a manifest depends on a file whose contents change more frequently than the agent receives new catalogs, a node might apply a version of the referenced file that doesn't match the instructions in the catalog. In Puppet Enterprise \(PE\), such situations are particularly likely if you’ve configured your agents to run off cached catalogs for participation in application orchestration services.

Consequently, the agent's Puppet runs might produce different results each time the agent applies the same catalog. This often causes problems because Puppet generally expects a catalog to produce the same results each time it's applied, regardless of any code or file content updates on the master.

Additionally, each time an agent applies a normal cached catalog that contains file resources sourced from `puppet:///` locations, the agent requests file metadata from the master each time the catalog's applied, even though nothing's changed in the cached catalog. This causes the master to perform unnecessary resource-intensive checksum calculations for each file resource.

Static catalogs avoid these problems by including metadata that refers to a specific version of the resource's file. This prevents a newer version from being incorrectly applied, and avoids having the agent regenerate the metadata on each Puppet run. The metadata is delivered in the form of a unique hash maintained, by default, by the file sync service.

We call this type of catalog "static" because it contains all of the information that an agent needs to determine whether the node's configuration matches the instructions and state of file resources at the static point in time when the catalog was generated.

### Differences in catalog behavior

Without static catalogs enabled:

-   The agent sends facts to the master and requests a catalog.
-   The master compiles and returns the agent's catalog.
-   The agent applies the catalog by checking each resource the catalog describes. If it finds any resources that are not in the desired state, it makes the necessary changes.

With static catalogs enabled:

-   The agent sends facts to the master and requests a catalog.
-   The master compiles and returns the agent's catalog, including metadata that that specifies the desired state of the node's file resources.
-   The agent applies the catalog by checking each resource the catalog describes. If the agent finds any resources that are not in the desired state, it makes the necessary changes based on the state of the file resources at the static point in time when the catalog was generated.
-   If you change code on the master, file contents are not updated until the agent requests a new catalog with new file metadata.

### Enabling file sync

In PE, static catalogs are disabled across all environments for new installations. To use static catalogs in PE, you must enable file sync. After file sync is enabled, Puppet Server automatically creates static catalogs containing file metadata for eligible resources, and agents running Puppet 1.4.0 or newer can take advantage of the catalogs' new features.

If you do not enable file sync and Code Manager, you can still use static catalogs, but you need to create some custom scripts and set a few parameters in the console.

### Enforcing change with static catalogs

When you are ready to deploy new Puppet code and deliver new static catalogs, you don’t need to wait for agents to check in. Use the Puppet orchestrator to enforce change and deliver new catalogs across your PE infrastructure, on a per-environment basis.

### When aren’t static catalogs applied?

In the following scenarios, either agents won't apply static catalogs, or catalogs won't include metadata for file resources.

-   Static catalogs are globally disabled.
-   Code Manager and file sync are disabled, and `code_id` and `code_content` aren't configured.
-   Your agents aren't running PE 2016.1 or later \(Puppet agent version 1.4.0 or later\).

Additionally, Puppet won't include metadata for a file resource if it:

-   Uses the `content` attribute instead of the `source` attribute.
-   Uses the `source` attribute with a non-Puppet scheme \(for example `source => 'http://host:port/path/to/file'`\).
-   Uses the `source` attribute without the built-in modules mount point.
-   Uses the `source` attribute, but the file on the master is not in `/etc/puppetlabs/code/environments/<environment>/*/*/files/**`. For example, module files are typically in `/etc/puppetlabs/code/environments/<environment>/modules/<module_name>/files/**`.

Agents continue to process the catalogs in these scenarios, but without the benefits of inlined file metadata or file resource versions.

**Related information**  


[Enabling or disabling file sync](filesync_about.md#)

[Running jobs with Puppet orchestrator](running_jobs_with_puppet_orchestrator_overview.md#)

## Disabling static catalogs globally with Hiera

You can turn off all use of static catalogs with a Hiera setting.

To disable static catalogs using Hiera:

1.  Add the following code to your default `.yaml` file and set the parameter to `false`:

    ```
    puppet_enterprise::master::static_catalogs: false
    ```

2.  To compile changes, run `puppet agent -t`


## Using static catalogs without file sync

To use static catalogs without enabling file sync, you must set the `code_id` and `code_content` parameters in Puppet, and then configure the `code_id_command`, `code_content_command`, and `file_sync_enabled` parameters in the console.

1.  Set `code_id` and `code_content` by following the instructions in the [open source Puppet static catalogs documentation](https://docs.puppet.com/puppet/5.3/static_catalogs.html#configuring-codeid-and-the-staticfilecontent-endpoint). Then return to this page to set the remaining parameters.

2.  In the console, click **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** node group.

3.  On the **Configuration** tab, locate the `puppet_enterprise::profile::master` class, and select **file\_sync\_enabled** from its **Parameter** list.

4.  In the **Value** field, enter false, and click **Add parameter**.

5.  Select the **code\_id\_command** parameter, and in the **Value** field, enter the absolute path to the `code_id` script, and click **Add parameter**.

6.  Select the **code\_content\_command** parameter, and in the **Value** field, add the absolute to the `code_content` script, and click **Add parameter**.

7.  Commit changes.

8.  Run Puppet on the master.


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

