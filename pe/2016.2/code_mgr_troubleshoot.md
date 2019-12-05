---
layout: default
title: "Troubleshooting Code Manager"
canonical: "/pe/latest/code_mgr_troubleshoot.html"
description: "Troubleshooting Code Manager, a tool for managing and deploying Puppet code in Puppet Enterprise."
---


[timeouts_deploy]: ./code_mgr_custom.html#timeoutsdeploy
[known_issues]: ./release_notes_known_issues_codemgmt.html
[webhook_auth]: ./code_mgr_custom.html#authenticatewebhook
[custom]: ./code_mgr_custom.html
[logs]: {{puppetserver}}/services_master_puppetserver.html#logging
[webhook]: ./code_mgr_webhook.html
[auth]: ./code_mgr_config.html#set-up-authentication-for-code-manager
[custom_r10k]: ./code_mgr_custom.html#r10k-specific-parameters

Code Manager requires coordination between multiple components, including r10k and the file sync service. If you are having problems with Code Manager, this page can help you isolate the problem.

## Code Manager logs

Code Manager logs to the Puppet Server log. By default, this log is in `/var/log/puppetlabs/puppetserver/puppetserver.log`. For more information about working with Puppet Server logs, see [Puppet Server log][logs] documentation.

## Common issues

Rule out these common issues before proceeding with more in-depth troubleshooting.

### Proxy issues

In releases before PE 2016.2, proxies were supported only for Forge modules. To use proxies to access modules from other sources, upgrade to PE 2016.2 or greater.

### Timeouts

If your environments are heavily loaded, code deployments can take a long time. If your deployments are timing out too soon, increase your [`timeouts_deploy` key][timeouts_deploy]. You might also need to increase `timeouts_shutdown` and `timeouts_wait`.

### File sync stops when Code Manager tries to deploy code

Code Manager code deployment involves accessing many small files. If you store your Puppet code on network-attached storage, slow network or backend hardware can result in poor deployment performance. Tune the network for many small files, or store Puppet code on local or direct-attached storage. <!--DOC-2680-->

### Missing classes after deployment

If your code deployment works, but a class you added isn't in the console:

1. Check on disk to verify that the environment folder exists.
2. Check your node group in the **Edit node group metadata** box to make sure it's using the correct environment.
2. Refresh classes.
3. Refresh your browser.

### File sync crashes on PE 2016.1.2 upgrade

When you upgrade to PE 2016.1.2, file sync might crash if you've deployed a large number of modules or environments:

* 100 - 300 modules
* 125 - 250 environments

With this issue, `puppetserver.log` receives a `LargeObjectException` and code will not deploy. In the console, Puppet Services status shows that Code Manager has an error condition.

For example, `/var/log/puppetlabs/puppetserver/puppetserver.log` shows something like this:

```
org.eclipse.jgit.errors.LargeObjectException: 30fba8f56386d7d2015fc6401a084466187ab520 exceeds size limit
    at org.eclipse.jgit.internal.storage.file.UnpackedObject$LargeObject.getCachedBytes(UnpackedObject.java:392) ~[puppet-server-release.jar:na]
    at org.eclipse.jgit.treewalk.CanonicalTreeParser.reset(CanonicalTreeParser.java:202) ~[puppet-server-release.jar:na]
    at org.eclipse.jgit.treewalk.CanonicalTreeParser.createSubtreeIterator0(CanonicalTreeParser.java:236) ~[puppet-server-release.jar:na]
    at org.eclipse.jgit.treewalk.CanonicalTreeParser.createSubtreeIterator(CanonicalTreeParser.java:214) ~[puppet-server-release.jar:na]
    at org.eclipse.jgit.treewalk.CanonicalTreeParser.createSubtreeIterator(CanonicalTreeParser.java:60) ~[puppet-server-release.jar:na]
    at org.eclipse.jgit.treewalk.TreeWalk.enterSubtree(TreeWalk.java:924) ~[
```

This error occurs because the `stream-file-threshold`, which sets the size of streaming files allowed by JGit, is too low for code deployment. Resolve the issue by setting the `stream-file-threshold` to 512, increasing the size of streaming files allowed by JGit.

1. Log into the master (or master of masters) as root.

2. Edit /etc/puppetlabs/puppetserver/conf.d/file-sync.conf to add the following to the client section: stream-file-threshold : 512

   For example:

    ```
    file-sync: {
      data-dir: "/opt/puppetlabs/server/data/puppetserver/filesync"
      client: {
        poll-interval: 5
        server-api-url: "https://puppet-master-ankeny:8140/file-sync/v1"
        server-repo-url: "https://puppet-master-ankeny:8140/file-sync-git"

        ssl-cert: "/etc/puppetlabs/puppet/ssl/certs/puppet-master-ankeny.pem"
        ssl-key: "/etc/puppetlabs/puppet/ssl/private_keys/puppet-master-ankeny.pem"
        ssl-ca-cert: "/etc/puppetlabs/puppet/ssl/certs/ca.pem"
        enable-forceful-sync : true
        stream-file-threshold : 512
      }
      ```

3. Restart pe-puppetserver by running:
   
   ```
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pe-puppetserver ensure=running
    ```
    
4. Repeat steps 1 through 3 on any compile masters that you have in your deployment. 

### Additional known issues

Check the [code management known issues][known_issues] page for additional known issues related to Code Manager, r10k, and file sync.

## Troubleshooting walk-through

To troubleshoot Code Manager further, follow these steps:

1. [Test the connection to the control repository.](#test-the-connection-to-the-control-repository)
2. [Check the Puppetfile for errors.](#check-the-puppetfile-for-errors)
3. [Run a deployment test.](#run-a-deployment-test)
4. [Log deployment trigger issues.](#log-deployment-trigger-issues)

### Test the connection to the control repository

The control repository controls the creation of Puppet environments, and ensures that the correct versions of all the necessary Puppet modules are installed. The Puppet master server must be able to access and clone the control repo as the `pe-puppet` user.

To make sure that Code Manager can connect to the control repo, run:

```shell
sudo -H -u pe-puppet bash -c \ "r10k deploy display --fetch -c /opt/puppetlabs/server/data/code-manager/r10k.yaml"
```

This command returns a list of the environments in the control repo.

If the command completes successfully, the SSH key has the correct permissions, the Git URL for the repository is correct, and the `pe-puppet` user can perform the operations involved.

If the command does not complete successfully, it returns a path that you can use for debugging the SSH key and Git URL.

### Check the Puppetfile for errors

Check the Puppetfile for syntax errors and verify that every module in the Puppetfile can be installed from the listed source. To do this, you'll need a copy of the Puppetfile in a temporary directory:

1. Create a temporary directory `/var/tmp/test-puppetfile` on the master for testing purposes.
2. Place a copy of the Puppetfile into the temporary directory.

You can then check the syntax and listed sources in your Puppetfile.

#### Check Puppetfile syntax

To check the Puppetfile syntax, run `r10k puppetfile check` from within the temporary directory.

If you have Puppetfile syntax errors, correct the syntax and test again. When the syntax is correct, the command prints "Syntax OK".

#### Check the sources listed in the Puppetfile

To test the configuration of all sources listed in your Puppetfile, perform a test installation. This test installs the modules listed in your Puppetfile into a modules directory in the temporary directory.

In the temporary directory, run the following command:

```shell
sudo -H -u pe-puppet bash -c \ "r10k puppetfile install"
```

This installs all modules listed in your Puppetfile, verifying that you can access all listed sources. Take note of **all** errors that occur. Issues with individual modules can cause issues for the entire environment. Errors with individual modules (such as Git URL syntax or version issues) show up as general errors for that module.

If you have modules from private Git repositories requiring an SSH key to clone the module, check that you are using the SSH Git URL and not the HTTPS Git URL.

After you've fixed errors, test again and fix any further errors, until all errors are fixed.

### Run a deployment test

Manually run a full r10k deployment to check not only the Puppetfile syntax and listed host access, but also whether the deployment will work through r10k.

This command attempts a full r10k deployment based on the `r10k.yaml` file that Code Manager uses. This test writes to the code-staging directory only. This does not trigger a file sync and should be used only for ad-hoc testing.

Run this deployment test with the following command:

```shell
  sudo -H -u pe-puppet bash -c \
    '/opt/puppetlabs/puppet/bin/r10k deploy environment -c /opt/puppetlabs/server/data/code-manager/r10k.yaml -p -v debug'
```

If this command completes successfully, the `/etc/puppetlabs/code-staging` directory is populated with directory-based environments and all of the necessary modules for every environment.

If the command fails, the error is likely in the r10k-specific [Code Manager settings][custom_r10k]. The error messages indicate which settings are failing.

### Log deployment trigger issues

Issues that occur when a Code Manager deployment is triggered by the webhook or the `/v1/deploys` endpoint can be tricky to isolate. Monitor the logs for the deployment trigger to find the issue.

#### Monitor webhook deployment log errors

Deployments triggered by a webhook in Stash/Bitbucket, GitLab, or Github are governed by authentication and hit each service's `/v1/webhook` endpoint.

If you are using a GitLab version less than 8.5.0, Code Manager webhook authentication does not work because of the length of the authentication token. To use a Code Manager webhook with GitLab, either [disable authentication][webhook_auth] or update Gitlab.

> **Note:** If you disable webhook authentication, it is disabled **only** for the `/v1/webhook` endpoint. Other endpoints (such as `/v1/deploys`) are still controlled by authentication. There is no way to disable authentication on any other Code Manager endpoint.

To troubleshoot webhook issues, follow the Code Manager [webhook instructions][webhook] while monitoring the Puppet Server log for successes and errors. To do this, open a terminal window and run:

```shell
  tail -f /var/log/puppetlabs/puppetserver/puppetserver.log
```

Watch the log closely for errors and information messages when you trigger the deployment. The `puppetserver.log` log file is the only location these errors will appear.

If you cannot determine the problem with your webhook in this step, manually deploy to the `/v1/deploys` endpoint, as described in the next section.

#### Monitor `/v1/deploys` endpoint log errors

Before you trigger a deployment to the `/v1/deploys` endpoint, generate an [authentication token][auth]. Then deploy one or more environments with the following command:

```shell
  curl -k -X POST -H 'Content-Type: application/json' \
    -H "X-Authentication: `cat ~/.puppetlabs/token`" \
    https://<URL.OF.CODE.MANAGER.SERVER>:8170/code-manager/v1/deploys \
    -d '{"environments": ["<ENV_NAME>"], "wait": true}'
```



This request waits for the deployment to complete (`"wait": true`) and so returns errors from the deployment.

Alternatively, you can deploy **all** environments with the following curl command:

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

