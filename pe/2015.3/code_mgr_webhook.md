---
layout: default
title: "Triggering Code Manager with a Webhook"
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


[token]: ./rbac_token_auth.html
[rbac_life]: ./rbac_token_auth.html#changing-the-default-lifetime
[rbac_perm]: ./rbac_permissions.html
[users_roles]: ./rbac_user_roles.html
[create_user]: ./rbac_user_roles.html#create-a-new-user
[config_access]: ./rbac_token_auth.html#configuring-puppet-access
[role_perm]: ./rbac_user_roles.html#assign-permissions-to-a-user-role
[add_user]: ./rbac_user_roles.html#add-a-user-to-a-user-role

To deploy your code, you'll need to trigger Code Manager by hitting a web endpoint. You can do this either through a webhook or a custom script. Either way, you'll also need to set up authentication and create a custom URL. 

The webhook, described on this page, is the simplest way to trigger Code Manager. Custom scripts are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications. See [Creating custom scripts][scripts] for information about writing a script to trigger Code Manager.

Code Manager supports webhooks for GitHub, Bitbucket Server (formerly Stash), Bitbucket, and Team Foundation Server. 

## Create a Webhook

To set up the webhook to trigger Code Manager to deploy your environments:

1. [Generate an authentication token](#generate-an-authentication-token) for authentication and authorization. 
2. [Create a custom URL](#create-a-custom-url) that triggers Code Manager.
3. [Set up the webhook](#set-up-the-webhook).

### Generate an Authentication Token

Code Manager needs an [authentication token][token] to use for both authentication and authorization. This token allows Code Manager to securely deploy the requested environment.

To generate a token for Code Manager:

1. [Create a deployment user role and user](#create-a-deployment-role-and-user).
2. [Request an authentication token](#request-an-authentication-token) for the deployment user. This step requires that you have already [configured the Puppet Access][config_access] command line tool.

> **Note to GitLab users:** Because GitLab has a character limit on webhooks, adding the token to the webhook won't work on GitLab. Instead of using a token, you should instead turn off authentication by [customizing your Code Manager configuration][code_mgr_custom] in Hiera with the `authenticate_webhook` parameter.

#### Create a deployment role and user

Before you request an authentication token, you must assign a user the correct permissions with role-based access control (RBAC). We recommend that you create a dedicated deployment user for Code Manager use. You can find detailed instructions for [creating users and user roles][users_roles] in the RBAC documentation.

To create the deployment user and user role:

1. [Create a new role][create_user] named "Deploy Environments".
2. [Assign this role][role_perm] the following permissions:
    * Add the **Puppet Environment** type.
      * Set **Permissions** for this type to **Deploy code**.
      * Set **Object** for this type to **All**.
    * Add the **Tokens** type.
      * Set **Permissions** for this type to **Override default expiry**.
3. [Create a deployment user][create_user].
4. [Add the deployment user][add_user] to the Deploy Environments role.

Next, request the token.

#### Request an authentication token

To request this token, you must have already [configured the Puppet Access][config_access] command line tool. 

Note that by default, authentication tokens have a five-minute lifetime. With the **Override default expiry** permission set, you can [change the lifetime][rbac_life] to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command:

1. On the command line on the master, run `puppet-access login --service-url https://<HOSTNAME OF PUPPET ENTERPRISE CONSOLE>:4433/rbac-api --lifetime 180d`. 

2. Enter the username and password of the deployment user when prompted.
   
The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`.

In the next step, you'll attach this token to a custom URL for your Code Manager trigger.

### Create a custom URL

To trigger Code Manager, you’ll need a custom URL composed of:

* The name of the Puppet master server (for example, `code-manager.example.com`)
* The Code Manager port (for example, 8170)
* The [endpoint](#webhook-endpoint) (`/code-manager/v1/webhook/`)
* Any relevant [query parameters](#query-parameters) (for example, `type=github`)
* The authentication token you generated earlier (`token=<TOKEN>`)

#### Webhook endpoint

The webhook endpoint triggers environment deployments that are based on version control system webhooks from the control repo. Pass the authentication token with the token query parameter. To use the Github webhook with the Puppet signed cert, disable SSL verification.

Code Manager supports webhooks for GitHub, Bitbucket Server (formerly Stash), Bitbucket, and Team Foundation Server (TFS). 

#### Query parameters

* `type`: Required. Specifies which type of post body to expect. Accepts:
    * `github`: GitHub
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

### Set up the webhook

Enter the URL you created above into your Git server’s webhook form as the payload URL. Exactly how you do this varies, depending on where your Git repos are hosted. For example, in your Github repo, click on **Settings > Webhooks & services** to enter the URL. Set the content type to JSON.

## Next Steps

At this point, your Code Manager setup is complete. Code Manager is ready to deploy your new code into your environments. Commit new code and push it to your control repo. The webhook triggers Code Manager and your code is deployed.
