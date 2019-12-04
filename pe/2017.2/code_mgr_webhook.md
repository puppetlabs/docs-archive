---
layout: default
title: "Triggering Code Manager with a webhook"
canonical: "/pe/latest/code_mgr_trigger.html"
description: "Setting up a webhook to trigger Puppet Code Manager"
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html
[upgrade]: ./code_mgr_upgrade.html
[filesync]: ./cmgmt_filesync.html
[troubleshoot]:./code_mgr_troubleshoot.html


[token]: ./rbac_token_auth.html
[rbac_life]: ./rbac_token_auth.html#changing-the-default-lifetime
[rbac_perm]: ./rbac_permissions.html
[users_roles]: ./rbac_user_roles.html
[create_user]: ./rbac_user_roles.html#create-a-new-user
[config_access]: ./rbac_token_auth.html#configuring-puppet-access
[role_perm]: ./rbac_user_roles.html#assign-permissions-to-a-user-role
[add_user]: ./rbac_user_roles.html#add-a-user-to-a-user-role

To deploy your code, you can trigger Code Manager by hitting a web endpoint, either through a webhook or a custom script. The webhook, described on this page, is the simplest way to trigger Code Manager.

Custom scripts are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications. See [Creating custom scripts][scripts] for information about writing a script to trigger Code Manager.

Before you begin, you should already have [enabled and configured][code_mgr_config] Code Manager.

Code Manager supports webhooks for GitHub, Bitbucket Server (formerly Stash), Bitbucket, and Team Foundation Server. The Code Manager webhook must only be used by the control repository. It can't be used by any other repository (for example, other internal component module repositories).

## Create a webhook

To set up the webhook to trigger Code Manager to deploy your environments:

1. [Create a custom URL](#create-a-custom-url) that triggers Code Manager.
2. [Set up the webhook](#set-up-the-webhook) with your Git host.

### Create a custom URL

To trigger Code Manager, you’ll need a custom URL composed of:

* The name of the Puppet master server (for example, `code-manager.example.com`).
* The Code Manager port (for example, 8170).
* The [endpoint](#webhook-endpoint) (`/code-manager/v1/webhook/`).
* Any relevant [query parameters](#query-parameters) (for example, `type=github`).
* The complete authentication token you generated earlier (`token=<TOKEN>`), passed with the `token` [query parameter](#query-parameters).

> **Note to GitLab users:** Prior to GitLab 8.5.0, GitLab had a character limit on webhooks and would not accept the entire authentication token. If you are using a GitLab version earlier than 8.5.0, either upgrade to the current GitLab version or turn off Code Manager webhook authentication [via Hiera][code_mgr_custom] with the `authenticate_webhook` parameter.

Code Manager supports webhooks for GitHub, Bitbucket Server (formerly Stash), Bitbucket, and Team Foundation Server (TFS). 

#### Query parameters

The following query parameters are permitted in the Code Manager webhook. The `token` query is mandatory, unless you disable `authenticate_webhook` in Code Manager's [configuration][code_mgr_custom].

* `type`: Required. Specifies which type of post body to expect. Accepts:
    * `github`: GitHub
    * `gitlab`: GitLab
    * `stash`: Bitbucket Server (Stash)
    * `bitbucket`: Bitbucket
    * `tfs-git`: Team Foundation Server (resource version 1.0 is supported)
* `prefix`: Specifies a prefix for converting branch names to environments.
* `token`: Specifies the entire PE authorization token. 

So, a URL for a GitHub webhook might look like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=github&token=<TOKEN>
```

The URL for a Stash webhook might look something like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=stash&prefix=dev&token=<TOKEN>
```

With the complete token attached, a GitHub URL looks something like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=github&token=eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkZXBsb3kiLCJpYXQiOjE0NDY2NjQ1OTAsImV4cCI6MTQ3ODI4Njk5MCwic3ViIjp7ImlkIjoiZWNmOTJlYzYtMjg1Zi00NzExLWI0MDktMzFmMGYzMGIzOGUwIiwibG9naW4iOiJkZXBsb3kifX0.OdEIA-HGRC0r2J87Pj2sDsyz-EMK-7sZalBTswy2e3uSv1Z6ulXaIQQd69PQnSSBExQotExDgXZInyQa_le2gwTo4smjUaBd6_lnPYr6GJ4hjB4-fT8dNnVuvZaE5WPkKt-sNJKJwE9LiEd4W42aCYfse1KNgPuXR5m77SRsUy86ymthVPKHqqexEyuS7LGeQJvyQE1qEejSdbiLg6znlJXhglWic6ZEpHcYBrFFE4aNwuOYXM4yIL803yIMviBwXRtt9HxbtUBZ8D4ag14ELMefQTlqrKvepKXd29oJG06FwkKcWV9PWiQ0Q-NrE17oxrrNkU0ZxioUgDeqGycwvNIaMazztM9NyD-dWmZc4dKJsqm0su0CRkMSWcYPPaeIcsYFI7XSaeC65N4RLIKhUfwIxxE-uODEhcl3mTr9rwZGnVMu3WrY7t6wlbDM9FomPejGM2aJoZ05PinAIYvX3oH5QJ9fam0pVLb-mI3bvkKm2wKAgpc4Dh1ut9sqLWkG8-AwMA4r_oEvLwFzf8clzk34zNyPG7BvlnPle99HjQues690L-fknSdFiXyRZeRThvZop0SWJzvUSR49etmk-OxnMbQE4tCBWZr_khEG5jUDzeKt3PIiXdxmUaaEPHzo6Vl9XIY5
```

## Set up the webhook

Enter the URL you created above into your Git server’s webhook form as the payload URL. The content type for webhooks is JSON.

Exactly how you set up the webhook varies, depending on where your Git repos are hosted. For example, in your GitHub repo, click on **Settings > Webhooks** to enter the payload URL and set the content type to `application/json`.

## Testing and troubleshooting

To test and troubleshoot your webhook, review the webhook runs logged by your repository host. Each of the major repository hosting services (such as GitHub or Bitbucket) provides a way to review your webhook runs, so check their documentation for instructions.

For other issues, check the Code Manager [troubleshooting][troubleshoot] for some common problems and troubleshooting tips.

## Next steps

At this point, your Code Manager setup is complete. Code Manager is ready to deploy your new code into your environments. Commit new code and push it to your control repo. The webhook triggers Code Manager, and your code is deployed.
