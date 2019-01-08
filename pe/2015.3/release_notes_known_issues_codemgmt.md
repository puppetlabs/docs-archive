---
layout: default
title: "Code Management Known Issues"
canonical: "/pe/latest/release_notes_known_issues_codemgmt.html"
---

[code_mgr_custom]: ./code_mgr_custom.html

As we discover them, this page will be updated with known issues related to Code Manager, r10k, and file sync in Puppet Enterprise 2015.3. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## Puppet Server might crash if code was previously deployed with r10k

Code Manager code deployment might crash Puppet Server if code has previously been deployed using r10k directly. To fix this issue, delete Git files left by r10k in the live code directory. You can do this with a command such as: 

`find /etc/puppetlabs/code -name '.git*' | xargs rm -rf`. 

## File sync overwrites hiera.yaml

When Code Manager runs, file sync overwrites the `hiera.yaml` file located in the codedir (`/etc/puppetlabs/code`). If you manage your `hiera.yaml` with Puppet, move this file to a location outside of the codedir. We suggest `/etc/puppetlabs/puppet/hiera.yaml`. <!--PE-13367--> <!--HI-490-->

## Vague Error Message for Insufficient Private Key Permissions

If the private key you specify during Code Manager configuration doesn't have sufficient permissions for Code Manager to access the file, a vague error message is logged after deployment:

~~~
2015-11-06 19:06:57,650 ERROR [deploy-pool-1] [p.c.core] Errors while deploying environment 'production' (exit code: 1):
ERROR    -> Unable to determine current branches for Git source 'puppet' (/etc/puppetlabs/code-staging/environments)
Original exception:
Failed to authenticate SSH session: Unable to extract public key from private key file: Unable to open private key file at /opt/puppetlabs/server/data/code-manager/worker-caches/deploy-pool-1/git@github.com-puppetlabs-codemgmt-92.git
~~~

To resolve this, make sure that the private key provided to Code Manager (in `r10k_private_key`) is accessible by the `pe-puppet` user.

## Do Not Use 'n' for File Sync Question in Answer File To Disable File Sync On Install 

If you're using an answer file to install PE 2015.3 and want to disable file sync, do not use `q_puppetmaster_file_sync_service_enabled=n` in the answer file, as doing so creates an issue in which the PE classification will fail and the node groups will not be properly populated. 

To workaround this issue, use `q_puppetmaster_file_sync_service_enabled=y`. After the installation completes, use the PE console to edit the `puppet_enterprise::profile::master` class and set the `file_sync_enabled` parameter to `false`.

## Turn Off Webhook Authentication for GitLab

Because GitLab has a character limit on webhooks, adding the token to the webhook won't work on GitLab. Instead of using an RBAC token, you should instead turn off authentication by [customizing your Code Manager configuration][code_mgr_custom] in Hiera with the `authenticate_webhook` parameter.

## Code Manager does not deploy modules with long filenames 

When installing Forge modules in which the path plus the file name in the module's tar file is longer than 100 characters, r10k does not copy the modules into the final module path. Because of this, Code Manager silently fails to deploy these modules into the `code-staging` directory.  <!--CODEMGMT-659-->


