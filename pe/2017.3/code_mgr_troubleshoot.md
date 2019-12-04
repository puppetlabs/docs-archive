# Troubleshooting Code Manager

Code Manager requires coordination between multiple components, including r10k and the file sync service. If you have issues with Code Manager, check that these components are functioning.

## Code Manager logs

Code Manager logs to the Puppet Server log. By default, this log is in `/var/log/puppetlabs/puppetserver/puppetserver.log`. For more information about working with the logs, see the Puppet Server [logs](https://docs.puppet.com/puppetserver/latest/services_master_puppetserver.html#logging) documentation.

## Check Code Manager status

Check the status of Code Manager and file sync if your deployments are not working as expected, or if you need to verify that Code Manager is enabled before running a dependent command.

The command `puppet-code status` verifies that Code Manager and file sync are responding. The command returns the same information as the Code Manager status endpoint. By default, the command returns details at the `info` level; `critical` and `debug` aren’t supported.

The following table shows errors that might appear in the `puppet-code status` output.

|Error|Cause|
|-----|-----|
|Code Manager couldn’t connect to the server|`pe-puppetserver` process isn’t running|
|Code Manager reports invalid configuration|Invalid configuration at `/etc/puppetlabs/puppetserver/conf.d/code-manager.conf`|
|File sync storage service reports unknown status|Status callback timed out|

## Test the connection to the control repository

The control repository controls the creation of environments, and ensures that the correct versions of all the necessary modules are installed. The master server must be able to access and clone the control repo as the `pe-puppet` user.

To make sure that Code Manager can connect to the control repo, run:

```
puppet-code deploy --dry-run
```

If the connection is set up correctly, this command returns a list of all environments in the control repo or repos. If the command completes successfully, the SSH key has the correct permissions, the Git URL for the repository is correct, and the `pe-puppet` user can perform the operations involved.

If the connection is not working as expected, Code Manager reports an `Unable to determine current branches for Git source` error.

The unsuccessful command also returns a path on the master or master of masters that you can use for debugging the SSH key and Git URL.

## Check the Puppetfile for errors

Check the Puppetfile for syntax errors and verify that every module in the Puppetfile can be installed from the listed source. To do this, you'll need a copy of the Puppetfile in a temporary directory.

Create a temporary directory `/var/tmp/test-puppetfile` on the master for testing purposes, and place a copy of the Puppetfile into the temporary directory.

You can then check the syntax and listed sources in your Puppetfile.

## Check Puppetfile syntax

To check the Puppetfile syntax, run `r10k puppetfile check` from within the temporary directory.

If you have Puppetfile syntax errors, correct the syntax and test again. When the syntax is correct, the command prints "Syntax OK".

## Check the sources listed in the Puppetfile

To test the configuration of all sources listed in your Puppetfile, perform a test installation. This test installs the modules listed in your Puppetfile into a modules directory in the temporary directory.

In the temporary directory, run the following command:

```shell
sudo -H -u pe-puppet bash -c \
  '/opt/puppetlabs/puppet/bin/r10k puppetfile install'
```

This installs all modules listed in your Puppetfile, verifying that you can access all listed sources. Take note of **all** errors that occur. Issues with individual modules can cause issues for the entire environment. Errors with individual modules \(such as Git URL syntax or version issues\) show up as general errors for that module.

If you have modules from private Git repositories requiring an SSH key to clone the module, check that you are using the SSH Git URL and not the HTTPS Git URL.

After you've fixed errors, test again and fix any further errors, until all errors are fixed.

## Run a deployment test

Manually run a full r10k deployment to check not only the Puppetfile syntax and listed host access, but also whether the deployment will work through r10k.

This command attempts a full r10k deployment based on the `r10k.yaml` file that Code Manager uses. This test writes to the code staging directory only. This does not trigger a file sync and should be used only for ad-hoc testing.

Run this deployment test with the following command:

```shell
  sudo -H -u pe-puppet bash -c \
    '/opt/puppetlabs/puppet/bin/r10k deploy environment -c /opt/puppetlabs/server/data/code-manager/r10k.yaml -p -v debug'
```

If this command completes successfully, the `/etc/puppetlabs/code-staging` directory is populated with directory-based environments and all of the necessary modules for every environment.

If the command fails, the error is likely in the Code Manager settings specific to r10k. The error messages indicate which settings are failing.

## Monitor logs for webhook deployment trigger issues

Issues that occur when a Code Manager deployment is triggered by the webhook can be tricky to isolate. Monitor the logs for the deployment trigger to find the issue.

Deployments triggered by a webhook in Stash/Bitbucket, GitLab, or GitHub are governed by authentication and hit each service's `/v1/webhook` endpoint.

If you are using a GitLab version older than 8.5.0, Code Manager webhook authentication does not work because of the length of the authentication token. To use the webhook with GitLab, either disable authentication or update GitLab.

**Note:** If you disable webhook authentication, it is disabled **only** for the `/v1/webhook` endpoint. Other endpoints \(such as `/v1/deploys`\) are still controlled by authentication. There is no way to disable authentication on any other Code Manager endpoint.

To troubleshoot webhook issues, follow the Code Manager webhook instructions while monitoring the Puppet Server log for successes and errors. To do this, open a terminal window and run:

```shell
  tail -f /var/log/puppetlabs/puppetserver/puppetserver.log
```

Watch the log closely for errors and information messages when you trigger the deployment. The `puppetserver.log` log file is the only location these errors will appear.

If you cannot determine the problem with your webhook in this step, manually deploy to the `/v1/deploys` endpoint, as described in the next section.

## Monitor logs for /v1/deploys deployment trigger issues

Issues that occur when a Code Manager deployment is triggered by the `/v1/deploys` endpoint can be tricky to isolate. Monitor the logs for the deployment trigger to find the issue.

Before you trigger a deployment to the `/v1/deploys` endpoint, generate an authentication token. Then deploy one or more environments with the following command:

```shell
  curl -k -X POST -H 'Content-Type: application/json' \
    -H "X-Authentication: `cat ~/.puppetlabs/token`" \
    https://<URL.OF.CODE.MANAGER.SERVER>:8170/code-manager/v1/deploys \
    -d '{"environments": ["<ENV_NAME>"], "wait": true}'
```

This request waits for the deployment and sync to compile masters to complete \(`"wait": true`\) and so returns errors from the deployment.

Alternatively, you can deploy **all** environments with the following `curl` command:

```shell
curl -k -X POST -H 'Content-Type: application/json' \
 -H "X-Authentication: `cat ~/.puppetlabs/token`" \
  https://<URL.OF.CODE.MANAGER.SERVER>:8170/code-manager/v1/deploys \
    -d '{"deploy-all": true, "wait": true}'
```

Monitor the `console-services.log` file for any errors that arise from this curl command.

```shell
  tail -f /var/log/pe-console-services/console-services.log
```

## Code deployments time out

If your environments are heavily loaded, code deployments can take a long time, and the system can time out before deployment is complete.

If your deployments are timing out too soon, increase your `timeouts_deploy` key. You might also need to increase `timeouts_shutdown`, `timeouts_sync`, and `timeouts_wait`.

## File sync stops when Code Manager tries to deploy code

Code Manager code deployment involves accessing many small files. If you store your Puppet code on network-attached storage, slow network or backend hardware can result in poor deployment performance.

Tune the network for many small files, or store Puppet code on local or direct-attached storage.

## Classes are missing after deployment

After a successful code deployment, a class you added isn't showing in the console.

If your code deployment works, but a class you added isn't in the console:

1.  Check on disk to verify that the environment folder exists.
2.  Check your node group in the **Edit node group metadata** box to make sure it's using the correct environment.
3.  Refresh classes.
4.  Refresh your browser.

