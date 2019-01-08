---
layout: default
title: "Triggering Code Manager with Custom Scripts"
canonical: "/pe/latest/code_mgr_scripts.html"
description: "Routes for writing custom scripts to trigger Code Manager for managing and deploying Puppet code."
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

Although the [webhook][code_mgr_webhook] is a simpler way to trigger Code Manager, custom scripts, described on this page, are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications.

To create a script to trigger Code Manager to deploy your environments:

1. [Generate an authentication token](#generate-an-authentication-token) for authentication and authorization. 
2. [Create a custom URL](#create-a-custom-url) that triggers Code Manager.
3. Write a script that hits [the deploys endpoint](#deploys-endpoint).

### Generate an authentication token

Code Manager needs an [authentication token][token] to use for both authentication and authorization. This token allows Code Manager to securely deploy the requested environment.

To generate a token for Code Manager:

1. [Create a deployment user role and user](#create-a-deployment-role-and-user).
2. [Request an authentication token](#request-an-authentication-token) for the deployment user. This step requires that you have already [configured the Puppet Access][config_access] command line tool.

#### Create a deployment role and user

Before you request a token, you must assign a user the correct permissions with role-based access control (RBAC). We recommend that you create a dedicated deployment user for Code Manager use. You can find detailed instructions for [creating users and user roles][users_roles] in the RBAC documentation.

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

## Create a Custom URL

To trigger Code Manager, you’ll need a custom URL composed of:

* Your Puppet master server name (for example, `localhost`)
* The Code Manager port (for example, 8170)
* The [endpoint](#deploys-endpoint) (`/code-manager/v1/deploys`)
* The authentication token you generated earlier (`token=<TOKEN>`)

The URL might look something like this: 

```
https://code-manager.example.com:8170/code-manager/v1/deploys&token=<TOKEN>
```

### Attaching the authentication token

You can either attach the entire token to your URL or you can specify the path to the token with `cat ~/.puppetlabs/token` in the body of your request, as shown in the examples below.


## Deploys Endpoint

To create custom scripts, use the `/deploys` endpoint, which provides a simple API for deploying code.

The `/deploys` endpoint queues Puppet code deployments. The body of the POST request must be a JSON object with one of the following structures:

#### The `"environments"` key

When you specify the `"environments"` key, Code Manager queues a deployment for each of the named environments.

The response contains a list of objects for each queued environment. Each object contains data about each environment.

For example, this statement:

```
    $ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"environments": ["production", "testing"]}'
```

results in the following response in JSON format:

```json
[
    {"environment": "production",
     "id": 1,
     "status": "pending"},
    {"environment": "testing",
     "id": 2,
     "status": "pending"}
```

#### The `"environments"` key with a wait

When you specify the `"wait"` key, Code Manager waits for the deployments on all environments to either finish or return an error before it returns its response.

The response contains a list of objects for each queued environment. Each
object contains data about each environment.


For example, this statement:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"environments": ["production"], "wait": true}'
```

results in the following response:

```json
[
  {
    "deploy-signature":"482f8d3adc76b5197306c5d4c8aa32aa8315694b",
    "file-sync":{
      "environment-commit":"6939889b679fdb1449545c44f26aa06174d25c21",
      "code-commit":"ce5f7158615759151f77391c7b2b8b497aaebce1"},
    "environment":"production",
    "id":3,
    "status":"complete"
  }
]
```

#### The `"deploy-all"` Key

When you specify the `"deploy-all"` key with a true value, Code Manager determines
the latest list of environments and queues a deployment for each environment. This
causes r10k to perform a `git fetch` of the backing Git repository.

When the `"wait"` key is given, Code Manager waits for the deployments on all environments to either finish or return an error before it returns its response.

For example, this script:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"deploy-all": true}'
```

results in a response with the formats described above.

## Next Steps

At this point, your Code Manager configuration is complete. Code Manager is ready to deploy your new code into your environments. Commit new code, push it to your control repo, and run your script to trigger Code Manager to deploy your code.
