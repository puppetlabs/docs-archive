---
layout: default
title: "Triggering Code Manager with custom scripts"
canonical: "/pe/latest/code_mgr_scripts.html"
description: "Routes for writing custom scripts to trigger Code Manager for managing and deploying Puppet code."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[cli]: ./code_mgr_cli.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[filesync]: ./cmgmt_filesync.html



Although the [webhook][code_mgr_webhook] is a simpler way to automatically trigger Code Manager, custom scripts, described on this page, are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications.

This page assumes that you have already [enabled and configured][code_mgr_config] Code Manager.

To create a script that triggers Code Manager to deploy your environments, you can use either the `puppet-code` command or a `curl` statement. We recommend the `puppet-code` command.

## Deploying environments with the `puppet-code` command

The `puppet-code` command allows you to trigger environment deployments from the command line. You can use this command in your custom scripts. For example, to deploy all environments and then return deployment results with commit SHAs for each of your environments, incorporate the following command into your script:

`puppet-code deploy --all --wait`

For details on `puppet-code` command configuration and use, please see the [`puppet-code` documentation][cli].

## Deploying environments with a `curl` command

To use a `curl` command in your custom script:

1. [Create a custom URL](#create-a-custom-url) to trigger Code Manager.
2. Compose a `curl` command to hit [the deploys endpoint](#deploys-endpoint).

### Create a custom URL

To trigger Code Manager, you’ll need a custom URL composed of:

* Your Puppet master server name (for example, `localhost`)
* The Code Manager port (for example, 8170)
* The [endpoint](#deploys-endpoint) (`/code-manager/v1/deploys`)
* The authentication token you generated earlier (`token=<TOKEN>`)

The URL might look something like this: 

```
https://code-manager.example.com:8170/code-manager/v1/deploys&token=<TOKEN>
```

#### Attaching the authentication token

You can either attach the entire token to your URL or you can specify the path to the token with `cat ~/.puppetlabs/token` in the body of your request, as shown in the examples below.

### Deploys endpoint

The `/deploys` endpoint, which provides a simple API for deploying code, queues Puppet code deployments. The body of the POST request must be a JSON object with one of the following structures:

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

The `deploy-signature` is the commit SHA of the control repo used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory.

#### The `"deploy-all"` key

When you specify the `"deploy-all"` key with a true value, Code Manager determines
the latest list of environments and queues a deployment for each environment. This
causes r10k to perform a `git fetch` of the backing Git repository.

When the `"wait"` key is given, Code Manager waits for the deployments on all environments to either finish or return an error before it returns its response.

For example, this script:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"deploy-all": true}'
```

results in a response with the formats described above.

## Next steps

At this point, your Code Manager configuration is complete. Code Manager is ready to deploy your new code into your environments. Commit new code, push it to your control repo, and run your script to trigger Code Manager to deploy your code.
