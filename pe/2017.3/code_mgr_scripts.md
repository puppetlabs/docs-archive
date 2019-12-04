# Triggering Code Manager with custom scripts

Custom scripts are a good way to trigger deployments if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications.

Alternatively, a webhook provides a simpler way to trigger deployments automatically.

To create a script that triggers Code Manager to deploy your environments, you can use either the `puppet-code` command or a `curl` statement. We recommend the `puppet-code` command.

Either way, after you have composed your script, you can trigger deployment of new code into your environments. Commit new code, push it to your control repo, and run your script to trigger Code Manager to deploy your code.

All of these instructions assume that you have enabled and configured Code Manager.

## Deploying environments with the puppet-code command

The `puppet-code` command allows you to trigger environment deployments from the command line. You can use this command in your custom scripts.

For example, to deploy all environments and then return deployment results with commit SHAs for each of your environments, incorporate this command into your script:

`puppet-code deploy --all --wait`

**Related topics**  


[Triggering Code Manager on the command line](puppet_code.md#)

## Deploying environments with a curl command

To use a `curl` command in your custom script, create a custom URL to trigger Code Manager and compose a `curl` command to hit the `deploys` endpoint.

To deploy environments with a `curl` command in your custom script, compose a `curl` command to hit the deploys endpoint. You must incorporate a custom URL for Code Manager in the script. 

### Creating a custom URL for Code Manager scripts

To trigger deployments with a `curl` command in a custom script, you’ll need a custom Code Manager URL.

This URL is composed of:

-   Your Puppet master server name \(for example, `localhost`\)
-   The Code Manager port \(for example, 8170\)
-   The deploys endpoint \(for example, `/code-manager/v1/deploys/`\)
-   The authentication token you generated earlier, if required for the endpoint \(`token=<TOKEN>`\)

A typical URL for use with a `curl` command might look something like this:

```
https://code-manager.example.com:8170/code-manager/v1/deploys&token=<TOKEN>
```

### Attaching an authentication token to a custom Code Manager URL

For your custom Code Manager URL to work, you must attach an authentication token to it.

When attaching the token for use with a `curl` command, you can either:

-   Attach the entire token to your URL.
-   Specify the path to the token with `cat ~/.puppetlabs/token` in the body of your request.

**Related topics**  


[Request an authentication token for deployments](code_mgr_config.md#)

### Code Manager deploys endpoint

The `/deploys` endpoint provides a simple API for deploying code and queues Puppet code deployments.

The body of the POST request must be a JSON object with one of the following structures:

-   The `"environments"` key, with or without the `wait` key.
-   The `"deploy-all"` key, with or without the `wait` key.
-   The `"dry_run"` key.

#### The "environments" key

When you specify the `"environments"` key, Code Manager queues a deployment for each of the named environments.

When the `"wait"` key is also given, Code Manager waits for the code deployments on all environments across all compile masters to either finish or return an error before it returns its response. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.

The response contains a list of objects for each queued environment. Each object contains data about each environment.

For example, this statement \(without the `wait` key\):

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

This statement \(\_with\_ the `wait` key\):

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
    "id":3,Code Manager
    "status":"complete"
  }
]
```

#### The "deploy-all" key

When you specify the `"deploy-all"` key with a true value, Code Manager determines the latest list of environments and queues a deployment for each environment. This causes r10k to perform a `git fetch` of the backing Git repository.

When the `"wait"` key is given, Code Manager waits for the code deployments on all environments across all compile masters to either finish or return an error before it returns its response. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.

For example, this script:

```
$ curl -k -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"deploy-all": true}'
```

results in a response with the formats described above.

#### The "dry\_run" key

When you specify the `“dry_run”` key with a true value, Code Manager tests the connections to each of the remotes configured in `code-manager.conf` and attempts to fetch a consolidated list of environments from all connected remotes. It reports any remote connection errors.

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

If it is unable to connect to the configured remote, it returns:

```json
{
    "details": "ERROR\t -> Unable to determine current branches for Git source 'puppet' (/etc/puppetlabs/code-staging/environments)\nOriginal exception:\nAuthentication failed for Git remote \"https://github.com/puppetlabs/control-ro\".\n",
    "kind": "puppetlabs.code-manager/collect-failed",
    "msg": "Errors while collecting a list of environments to deploy (exit code: 1)"
}
```

## Checking deployment status with a curl commmand

You can check the status of a deployment by hitting the `status` endpoint.

To use a `curl` command in your custom script, compose a `curl` command to hit the status endpoint. You must incorporate a custom URL for Code Manager in the script. 

### Code Manager status endpoint

The `/status` endpoint reports whether Code Manager and file sync are responding.

By default, details are returned at the `info` level. The `critical` and ``debug`` levels aren’t supported. The body of the GET request must be a JSON object.

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

