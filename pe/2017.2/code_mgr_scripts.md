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


Custom scripts, described on this page, are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications. Alternatively, a [webhook][code_mgr_webhook] provides a simpler way to trigger Code Manager automatically.

Before you begin, you should have already [enabled and configured][code_mgr_config] Code Manager.

To create a script that triggers Code Manager to deploy your environments, you can use either the `puppet-code` command or a `curl` statement. We recommend the `puppet-code` command.

## Deploying environments with the `puppet-code` command

The `puppet-code` command allows you to trigger environment deployments from the command line. You can use this command in your custom scripts. For example, to deploy all environments and then return deployment results with commit SHAs for each of your environments, incorporate the following command into your script:

`puppet-code deploy --all --wait`

For details on `puppet-code` command configuration and use, see the [`puppet-code` documentation][cli].

## Deploying environments with a `curl` command

To use a `curl` command in your custom script:

1. [Create a custom URL](#create-a-custom-url) to trigger Code Manager.
2. Compose a `curl` command to hit [the deploys endpoint](#deploys-endpoint).

### Creating a custom URL

To trigger Code Manager, you’ll need a custom URL composed of:

* Your Puppet master server name (for example, `localhost`)
* The Code Manager port (for example, 8170)
* The deploys [endpoint](#deploys-endpoint) (for example, `/code-manager/v1/deploys/`)
* The authentication token you generated earlier, if required for the endpoint (`token=<TOKEN>`)

The URL might look something like this:

```
https://code-manager.example.com:8170/code-manager/v1/deploys&token=<TOKEN>
```

#### Attaching the authentication token

You must attach the authentication token to your custom URL. You can either: 

* Attach the entire token to your URL.
* Specify the path to the token with `cat ~/.puppetlabs/token` in the body of your request.

The examples below show the token path specified with `cat ~/.puppetlabs/token`.

### Deploys endpoint

The `/deploys` endpoint, which provides a simple API for deploying code, queues Puppet code deployments. The body of the POST request must be a JSON object with one of the following structures:

* The [`"environments"`](#the-environments-key) key, with or without the `wait` key.
* The [`"deploy-all"`](#the-deploy-all-key) key, with or without the `wait` key.
* The [`"dry_run"`](#the-dry-run-key) key.

#### The `"environments"` key

When you specify the `"environments"` key, Code Manager queues a deployment for each of the named environments.

When the `"wait"` key is also given, Code Manager waits for the code deployments on all environments across all compile masters to either finish or return an error before it returns its response. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes. 

The response contains a list of objects for each queued environment. Each object contains data about each environment.

For example, this statement (without the `wait` key):

```
    $ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"environments": ["production", "testing"]}'
```

results in the following response in JSON format, showing the deployment queue:

```json
[
    {"environment": "production",
     "id": 1,
     "status": "pending"},
    {"environment": "testing",
     "id": 2,
     "status": "pending"}
```

This statement (_with_ the `wait` key):

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"environments": ["production"], "wait": true}'
```

results in the following response, which includes a `deploy-signature`. 

The `deploy-signature` is the commit SHA of the control repo used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory.

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

#### The `"deploy-all"` key

When you specify the `"deploy-all"` key with a true value, Code Manager determines
the latest list of environments and queues a deployment for each environment. This
causes r10k to perform a `git fetch` of the backing Git repository.

When the `"wait"` key is given, Code Manager waits for the deployments on all environments across all compile masters to either finish or return an error before it returns its response. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes. 

For example, this script:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"deploy-all": true}'
```

results in a response with the formats described above.

#### The `"dry_run"` key

When you specify the `“dry_run”` key with a true value, Code Manager tests the connections to each of the remotes configured in code-manager.conf and attempts to fetch a consolidated list of environments from all connected remotes. It will report any remote connection errors.

For example, if this script:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"dry_run": true}'
```

connects successfully to the configured remote, it returns:

```json
[
    {
        "environment": "development"
    },
    {
        "environment": "master"
    },
    {
        "environment": "production"
    },
    {
        "environment": "testing"
    }
]
```

but if it is unable to connect to the configured remote, it returns:

```json
{
    "details": "ERROR\t -> Unable to determine current branches for Git source 'puppet' (/etc/puppetlabs/code-staging/environments)\nOriginal exception:\nAuthentication failed for Git remote \"https://github.com/puppetlabs/control-ro\".\n",
    "kind": "puppetlabs.code-manager/collect-failed",
    "msg": "Errors while collecting a list of environments to deploy (exit code: 1)"
}
```

## Checking deployment status with a curl commmand

To use a `curl` command in your custom script:

1. [Create a custom URL](#create-a-custom-url) to trigger Code Manager.
2. Compose a `curl` command to hit [the status endpoint](#status-endpoint).

### Status endpoint

The `/status` endpoint reports whether Code Manager and file sync are responding. By default, details are returned at the `info` level; `critical` and `debug` aren’t supported. The body of the GET request must be a JSON object.

For example, this statement:

```
    $ curl -k -X GET -H 'Content-Type: application/json' https://localhost:8170/code-manager/v1/status?level=info
```

results in the following response:

```json
[
  {
    "state":"running","status":
    {"file_sync":"enabled","services":{"file-sync-storage":"running"}},"alerts":
    [],"detail_level":"info"
  }
]
```


## Next steps

At this point, your Code Manager configuration is complete. Code Manager is ready to deploy your new code into your environments. Commit new code, push it to your control repo, and run your script to trigger Code Manager to deploy your code.
