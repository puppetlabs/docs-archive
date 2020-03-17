# About file sync

File sync helps Code Manager keep your Puppet code synchronized across multiple masters.

When triggered by a web endpoint, file sync takes changes from your working directory on your master and deploys the code to a live code directory. File sync then automatically deploys that code onto all your compilers, ensuring that all masters in a multi-master configuration are kept in sync.

In addition, file sync ensures that your Puppet code is deployed only when it is ready. These deployments ensure that your agents' code won't change during a run. File sync also triggers an environment cache flush when the deployment has finished, to ensure that new agent runs happen against the newly deployed Puppet code.

File sync works with Code Manager, so you typically won't need to do anything with file sync directly. If you want to know more about how file sync works, or you need to work with file sync directly for testing, this page provides additional information.

**Parent topic:**[Managing and deploying Puppet code](managing_puppet_code.md)

## File sync terms

There are a few terms that are helpful when you are working with file sync or tools that use file sync.

-   ****Live code directory****

    This is the directory that all of the Puppet environments, manifests, and modules are stored in. This directory is used by Puppet Server for catalog compilation. It corresponds to the Puppet Server `master-code-dir` setting and the Puppet`$codedir` setting. The default value is `/etc/puppetlabs/code`. In file sync configuration, you might see this referred to simply as live-dir. This directory exists on all of your masters.

-   ****Staging code directory****

    This is the directory where you stage your code changes before rolling them out to the live code dir. You can move files into this directory in your usual way. Then, when you trigger a file sync deployment, file sync moves the changes to the live code dir on all of your masters. This directory exists only on the master; compilers do not need a staging directory. The default value is `/etc/puppetlabs/code-staging`.


## How file sync works

File sync helps distribute your code to all of your masters and agents.

By default, file sync is disabled and the staging directory is not created on the master. If you're upgrading from 2015.2 or earlier, file sync is disabled after the upgrade. You must enable file sync, and then run Puppet on all masters. This creates the staging directory on the master, which you can then populate with your Puppet code. File sync can then commit your code; that is, it can prepare the code for synchronization to the live code directory, and then your compilers. Normally, Code Manager triggers this commit automatically, but you can trigger a commit by hitting the file sync endpoint.

For example:

```bash
/opt/puppetlabs/puppet/bin/curl -s --request POST --header "Content-Type: application/json" --data '{"commit-all": true}' --cert ${cert} --key ${key} --cacert ${cacert} https://${fqdn}:8140/file-sync/v1/commit"
```

The above command is run from the master and contains the following variables:

-   `fqdn`: Fully qualified domain name of the master.
-   `cacert`: The Puppet CA's certificate \(`/etc/puppetlabs/puppet/ssl/certs/ca.pem`\).
-   `cert`: The ssl cert for the master \(`/etc/puppetlabs/ssl/certs/${fqdn}.pem`\).
-   `key`: The private key for the master \(`/etc/puppetlabs/ssl/private_keys/${fqdn}.pem`\).

This command commits all of the changes in the staging directory. After the commit, when any compilers check the file sync service for changes, they receive the new code and deploy it into their own code directories, where it is available for agents checking in to those masters. \(By default, compilers check file sync every 5 seconds.\)

Commits can be restricted to a specific environment and can include details such as a message, and information about the commit author.

### Enabling or disabling file sync

File sync is normally enabled or disabled automatically along with Code Manager.

File sync's behavior is linked to that of Code Manager. Because Code Manager is disabled by default, file sync is also disabled. To enable file sync, enable Code Manager You can enable and configure Code Manager either during or after PE installation.

The `file_sync_enabled` parameter in the `puppet_enterprise::profile::master` class in the console defaults to `automatic`, which means that file sync is enabled and disabled automatically with Code Manager. If you set this parameter to `true`, it forces file sync to be enabled even if Code Manager is disabled. The `file_sync_enabled` parameter doesn't appear in the class definitions --- you must add the parameter to the class in order to set it.

### Resetting file sync

If file sync has entered a failure state, consumed all available disk space, or a repository has become irreparably corrupted, reset the service.

Resetting deletes the commit history for all repositories managed by file sync, which frees up disk space and returns the service to a "fresh install" state while preserving any code in the staging directory.

1.  On the master, perform the appropriate action:

    -   If you use file sync with Code Manager, ensure that any code ready for deployment is present in the staging directory, and that the code most recently deployed is present in your control repository so that it can be re-synced.

    -   If you use file sync with r10k, perform an r10k deploy and ensure that the code most recently deployed is present in your control repository so that it can be re-synced.

    -   If you use file sync alone, ensure that any code ready for deployment is present in `/etc/puppetlabs/code-staging`.

2.  Shut down the pe-puppetserver service: `puppet resource service pe-puppetserver ensure=stopped`.

3.  Delete the data directory located at `/opt/puppetlabs/server/data/puppetserver/filesync/storage`.

4.  Restart the pe-puppetserver service: `puppet resource service pe-puppetserver ensure=running`.

5.  Perform the appropriate action:

    -   If you use file sync with Code Manager, use Code Manager to deploy all environments.

    -   If you use file sync alone or with r10k, perform a commit.


File sync is now reset. The service creates fresh repositories on each client and the storage server for the code it manages.

## Checking your deployments

You can manually check information about file sync's deployments with curl commands that hit the `status` endpoint.

To manually check that your code has been successfully committed and deployed, you can hit a status endpoint on the master.

```
curl -k https://${fqdn}:8140/status/v1/services?level=debug

```

This returns output in JSON, so you can pipe it to `python -m json.tool` for better readability.

To check a list of file sync's clients, query the `file-sync-storage-service` section of the master by running a curl command.

This command returns a list of:

-   All of the clients that file sync is aware of.
-   When those clients last checked in.
-   Which commit they have deployed .

```
curl -k https://${fqdn}:8140/status/v1/services/file-sync-client-service?level=debug

```

If your commit has been deployed, it is listed in the status listing for `latest_commit`.

## Cautions

There are a few things to be aware of with file sync.

### Always use the staging directory

Always make changes to your Puppet code in the staging directory. If you have edited code in the live code directory on the master or any compiled masters, *it's overwritten* by file sync on the next commit.

The `enable-forceful-sync` parameter is set to `true` by default in PE. When `false`, file sync does not overwrite changes in the code directory, but instead logs errors in `/var/log/puppetlabs/puppetserver/puppetserver.log`. To set this parameter to `false`, add via Hiera \(`puppet_enterprise::master::file_sync::file_sync_enable_forceful_sync: false`\).

### The puppet module command and file sync

The `puppet module` command doesn't work with file sync. If you are using file sync, specify modules in the Puppetfile and use Code Manager to handle your syncs.

### Permissions

File sync runs as the `pe-puppet` user. To sync files, file sync **must** have permission to read the staging directory and to write to all files and directories in the live code directory. To make sure file sync can read and write what it needs to, ensure that these code directories are both owned by the `pe-puppet` user:

```bash
chown -R pe-puppet /etc/puppetlabs/code /etc/puppetlabs/code-staging
```

### Environment isolation metadata

File sync generates `.pp` metadata files in both your live and staging code directories. These files provide environment isolation for your resource types, ensuring that each environment uses the correct version of the resource type. Do not delete or modify these files. Do not use expressions from these files in regular manifests.

For more details about these files and how they isolate resource types in multiple environments, see environment isolation.

