---
author: Jean Bond <jean@puppet.com\>
---

# Code Manager API

Use Code Manager endpoints to deploy code and query environment deployment status on your masters without direct shell access.

## Authentication

All Code Manager endpoint requests require token-based authentication. For `/deploys` endpoints, you can pass this token as either an HTTP header or as a query parameter. For the `/webhook` endpoint, you must attach the entire token with the `token` query parameter.

To generate an authentication token, from the command line, run `puppet-access login`. This command stores the token in `~/.puppetlabs/token`. To learn more about authentication for Code Manager use, see the related topic about requesting an authentication token.

Pass this token as either an `X-Authentication` HTTP header or as the `token` query parameter with your request. If you pass the token as a query parameter, it might show up in access logs.

For example, an HTTP header for the `/deploys` endpoint looks something like:

```
curl --cacert $(puppet agent --configprint cacert) -X POST -H 'Content-Type: application/json' \-H "X-Authentication: `puppet access show`" \"https://localhost:8170/code-manager/v1/deploys" \-d '{"environments": ["production"]}'
```

Passing the token with the `token` query parameter might look like:

```
curl --cacert $(puppet agent --configprint cacert) -X POST -H 'Content-Type: application/json' \"https://localhost:8170/code-manager/v1/webhook?type=github&token=`cat ~/.puppetlabs/token`" \-d $GITHUB_PAYLOAD
```

**Related information**  


[Request an authentication token for deployments](code_mgr_config.md#)

[API index](api_index.md#)

## POST `/v1/deploys`

Use the Code Manager`/deploys` endpoint to queue code deployments.

### Request format

The body of the `POST /deploys` request must be a JSON object that includes the authentication token and a URL containing:

-   The name of the master server, such as `master.example.com`

-   The Code Manager port, such as 8170.

-   The endpoint.


The request must also include either:

-   The `"environments"` key, with or without the `"wait"` key.

-   The `"deploy-all"` key, with or without the `"wait"` key.


|Key|Definition|Values|
|---|----------|------|
|`"environments"`|Specifies the environments for which to queue code deployments. If not specified, the `"deploy-all"` key must be specified.|Accepts a comma- and space-separated list of strings specifying environments.|
|`"deploy-all"`|Specifies whether Code Manager should deploy all environments.

 If specified `true`, Code Manager determines the most recent list of environments and queues a deploy for each one. If specified `false` or not specified, the `"environments"` key must be specified with a list of environments.

|`true`, `false`|
|`"wait"`|Specifies whether Code Manager should wait for all deploys to finish or error before it returns a response.

 If specified `true`, Code Manager returns a response after all deploys have either finished or errored. If specified `false` or not specified, returns a response showing that deploys are queued.

 Specify together with the `"deploy-all"` or `"environment"` keys.

|`true`, `false`|
|`"dry-run"`|Tests the  Code Manager connection to each of the configured remotes and attempts to fetch a list of environments from each, reporting any connection errors.

 Specify together with the `"deploy-all"` or `"environment"` keys.

|`true`, `false`|

For example, to deploy the production and testing environments and return results after the deployments complete or fail, pass the `"environments"` key with the `"wait"` key:

```
curl --cacert $(puppet agent --configprint cacert) -X POST -H 'Content-Type: application/json' \-H "X-Authentication: `cat ~/.puppetlabs/token`" \"https://localhost:8170/code-manager/v1/deploys" \-d '{"environments": ["production", "testing"], "wait": true}'
```

To deploy all configured environments and return only the queuing results, pass the `"deploy-all"` key without the `"wait"` key:

```
curl --cacert $(puppet agent --configprint cacert) -X POST -H 'Content-Type: application/json' \-H "X-Authentication: `cat ~/.puppetlabs/token`" \"https://localhost:8170/code-manager/v1/deploys" \-d '{"deploy-all": true}'
```

### Response format

The `POST /deploys` response contains a list of objects, each containing data about a queued or deployed environment.

If the request did not include a `"wait"` key, the response returns each environment's name and status.

If the `"wait"` key was included in the request, the response returns information about each deployment. This information includes a `deploy-signature`, which is the commit SHA of the control repo that Code Manager used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been deployed to the code staging directory.

The response shows any deployment failures, even after they have successfully deployed. The failures remain in the response until cleaned up by garbage collection.

|Key|Description|Value|
|---|-----------|-----|
|`"environment"`|The name of the environment queued or deployed.|A string specifying the name of an environment.|
|`"id"`|Identifies the queue order of the code deploy request.|An integer generated by Code Manager.|
|`"status"`|The status of the code deployment for that environment.|Can be one of the following:

 -   `"new"`: The deploy request has been accepted but not yet queued.

-   `"complete"`: The deploy is complete and has been synced to the live code directory on masters.

-   `"failed"`: The deploy failed.

-   `"queued"`: The deploy is queued and waiting.


|
|`"deploy-signature"`|The commit SHA of the control repo that Code Manager used to deploy code in that environment.|A Git SHA. |
|`"file-sync"`|Commit SHAs used internally by file sync to identify the code synced to the code staging directory|Git SHAs for the `"environment-commit"` and `"code-commit"` keys. |

For example, for a `/deploys` request without the `"wait"` key,  the response shows only `"new"` or `"queued"` status, because this response is returned as soon as Code Manager accepts the request.

```
[
  {
    "environment": "production",
    "id": 1,
    "status": "queued"
  },
  {
    "environment": "testing",
    "id": 2,
    "status": "queued"
  }
]
```

If you pass the `"wait"` key with your request, Code Manager doesn't return any response until the environment deployments have either failed or completed. For example:

```
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

### Error responses

If any deployments fail, the response includes an error object for each failed environment. Code Manager returns deployment results only if you passed the `"wait"` key; otherwise, it returns queue information.

|Key|Definition|Value|
|---|----------|-----|
|`"environment"`|The name of the environment queued or deployed.|A string specifying the name of an environment.|
|`"error"`|Information about the deployment failure.|Contains keys with error details.|
|`"corrected-env-name"`|The name of the environment.|A string specifying the name.|
|`"kind"`|The kind of error encountered.|An error type.|
|`"msg"`|The error message.|An error message.|
|`"id"`|Identifies the queue order of the code deploy request.|An integer generated by Code Manager.|
|`"status"`|The status of the requested code deployment.|For errors, status is `"failed"`.|

For example, information for a failed deploy of an environment might look like:

```
    {
         "environment": "test14",
         "error": {
             "details": {
                 "corrected-env-name": "test14"
             },
             "kind": "puppetlabs.code-manager/deploy-failure",
             "msg": "Errors while deploying environment 'test14' (exit code: 1):\nERROR\t -> Authentication failed for Git remote \"https://github.com/puppetlabs/puffppetlabs-apache\".\n"
         },
         "id": 52,
         "status": "failed"
     }
```

## POST `/v1/webhook`

Use the Code Manager`/webhook` endpoint to trigger code deploys based on your control repo updates.

### Request format

The `POST /webhook` request consists of a specially formatted URL that specifies the webhook type, an optional branch prefix, and a PE authentication token.

You must pass the authentication token with the `token` query parameter. To use a GitHub webhook with the Puppet signed certificate, you must disable SSL verification.

In your URL, include:

-   The name of the Puppet master server \(for example, `master.example.com`\)

-   The Code Manager port \(for example, 8170\)

-   The endpoint \(`/v1/webhook`\)

-   The `type` parameter with a valid value, such as `gitlab`.

-   The `prefix` parameter and value.

-   The `token` parameter with the complete token.


|Parameter|Definition|Values|
|---------|----------|------|
|`type`|Required. Specifies what type of POST body to expect.|-   `github`

-   `gitlab`

-   `tfs-git`

-   `bitbucket`

-   `stash`


|
|`prefix`|Optional. Specifies a prefix to use in translating branch names to environments.|A string specifying a branch prefix.|
|`token`|Specifies the PE authorization token to use. Required unless you disable `webhook_authentication` in Code Manager configuration.|The complete authentication token.|

For example, a GitHub webhook might look like this:

```
https://master.example.com:8170/code-manager/v1/webhook?type=github&token=$TOKEN

```

A Stash webhook with the optional `prefix` parameter specifed might look like:

```
https://master.example.com/code-manager/v1/webhook?type=stash&prefix=dev&token=$TOKEN
```

You must attach the complete authentication token to the request. A GitHub request with the entire token attached might look like this:

```
https://master.example.com:8170/code-manager/v1/webhook?type=github&token=eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkZXBsb3kiLCJpYXQiOjE0NDY2NjQ1OTAsImV4cCI6MTQ3ODI4Njk5MCwic3ViIjp7ImlkIjoiZWNmOTJlYzYtMjg1Zi00NzExLWI0MDktMzFmMGYzMGIzOGUwIiwibG9naW4iOiJkZXBsb3kifX0.OdEIA-HGRC0r2J87Pj2sDsyz-EMK-7sZalBTswy2e3uSv1Z6ulXaIQQd69PQnSSBExQotExDgXZInyQa_le2gwTo4smjUaBd6_lnPYr6GJ4hjB4-fT8dNnVuvZaE5WPkKt-sNJKJwE9LiEd4W42aCYfse1KNgPuXR5m77SRsUy86ymthVPKHqqexEyuS7LGeQJvyQE1qEejSdbiLg6znlJXhglWic6ZEpHcYBrFFE4aNwuOYXM4yIL803yIMviBwXRtt9HxbtUBZ8D4ag14ELMefQTlqrKvepKXd29oJG06FwkKcWV9PWiQ0Q-NrE17oxrrNkU0ZxioUgDeqGycwvNIaMazztM9NyD-dWmZc4dKJsqm0su0CRkMSWcYPPaeIcsYFI7XSaeC65N4RLIKhUfwIxxE-uODEhcl3mTr9rwZGnVMu3WrY7t6wlbDM9FomPejGM2aJoZ05PinAIYvX3oH5QJ9fam0pVLb-mI3bvkKm2wKAgpc4Dh1ut9sqLWkG8-AwMA4r_oEvLwFzf8clzk34zNyPG7BvlnPle99HjQues690L-fknSdFiXyRZeRThvZop0SWJzvUSR49etmk-OxnMbQE4tCBWZr_khEG5jUDzeKt3PIiXdxmUaaEPHzo6Vl9XIY5
```

### Response format

When you trigger your webhook by pushing changes to your control repository, you can see the response in your Git provider interface.  Code Manager does not give a command line response to typical webhook usage.

If you hit the webhook endpoint directly using a curl command with a properly formatted request, valid authentication token, and a valid value for the `"type"` parameter, Code Manager returns a `{"status":"OK"}` response, whether or not code successfully deployed.

### Error responses

When you trigger your webhook by pushing changes to your control repository, you can see any errors in your Git provider interface.   Code Manager does not give a command line response to typical webhook usage.

If you hit the webhook endpoint directly using a curl command with a request that has an incorrect `"type"` value or no `"type"` value, you get an error response such as:

```
{"kind":"puppetlabs.code-manager/unrecognized-webhook-type","msg":"Unrecognized webhook type: 'githubby'","details":"Currently supported valid webhook types include: github gitlab stash tfs-git bitbucket"}
```

## GET `/v1/deploys/status`

Use the Code Manager`/deploys/status` endpoint to return the status of the code deployments that Code Manager is processing for each environment.

### Request format

The body of the `GET /deploys/status` request must include the authentication token and a URL containing:

-   The name of the master server, such as `master.example.com`.

-   The Code Manager port, such as 8170.

-   The endpoint.


```
curl --cacert $(puppet agent --configprint cacert) -X GET -H 'Content-Type: application/json' \-H "X-Authentication: `cat ~/.puppetlabs/token`" \"https://localhost:8170/code-manager/v1/deploys/status"
```

### Response format

The `/deploys/status` endpoint responds with a list of the code deployment requests that Code Manager is processing.

The response contains three sections:

-   `"deploys-status"`: Lists the status for each code deployment that Code Manager is processing, including failed deployments. Deployments can be `"queued"`, `"deploying"`, `"new"`, or `"failed"`. Environments that have been successfully deployed to either the code staging or live code directories are displayed in the `"file-sync-storage-status"` or `"file-sync-client-status"` sections, respectively.

-   `"file-sync-storage-status"`: Lists all environments that Code Manager has successfully deployed to the code staging directory, but not yet synced to the live code directory.

-   `"file-sync-client-status"`: Lists status for each master that Code Manager is deploying environments to, including whether the code in the master's staging directory has been synced to its live code directory.


The response can contain the following keys:

|Key|Definition|Values|
|---|----------|------|
|`"queued"`|The environment is queued for deployment.|For each environment: -   `"environment"`

-   `"queued-at"`


|
|`"deploying"`|The environment is in the process of being deployed.|For each environment: -   `"environment"`

-   `"queued-at"`


|
|`"new"`|Code Manager has accepted a request to deploy the environment, but has not yet queued it.|For each environment: -   `"environment"`

-   `"queued-at"`


|
|`"failed"`|The code deployment for the environment has failed.|For each environment:

 -   `"environment"`

-   `"error"`

-   `"details"`

-   `"corrected-env-name"`

-   `"kind"`

-   `"msg"`

-   `"queued-at"`


|
|`"environment"`|The name of the environment.|A string that is the name of the environment.|
|`"queued-at"`|The date and time when the environment was queued.|A date and time stamp.|
|`"deployed"`|Lists information for each deployed environment.|For each environment: -   `"environment"`

-   `"queued-at"`


|
|`"date"`|The date and time the operation was completed.|A date and time stamp.|
|`"deploy-signature"`|The commit SHA of the control repo used to deploy the environment.|A Git SHA.|
|`"all-synced"`|Whether all requested code deployments have been synced to the live code directories on their respective masters.|`true`, `false`|
|`"file-sync-clients"`|List of all masters that Code Manager is deploying code to.|For each environment listed: -   `"last_check_in_time"`

-   `"synced-with-file-sync-storage"`

-   `"deployed"`

-   `"environment"`

-   `"date"`

-   `"deploy-signature"`


|
|`"last_check_in_time"`|The most recent time that the live code directory checked in for new code from the staging directory.|A date and time stamp.|
|`"synced-with-file-sync-storage"`|Whether the live code directory has been synced with the code staging directory on a given master.|`true`, `false`|

For example, a complete response to a request might look like:

```
{
  "deploys-status":{
    "queued":[
        {
         "environment":"dev",
         "queued-at":"2018-05-15T17:42:34.988Z"
        }
      ],
      "deploying":[
        {
          "environment":"test",
          "queued-at":"2018-05-15T17:42:34.988Z"
         },
         {
           "environment":"prod",
           "queued-at":"2018-05-15T17:42:34.988Z"
         }
      ],
      "new":[    
      
      ],
      "failed":[

      ]
     },
     "file-sync-storage-status":{
       "deployed":[
         {
           "environment":"prod",
           "date":"2018-05-10T21:44:24.000Z",
           "deploy-signature":"66d620604c9465b464a3dac4884f96c43748b2c5"
         },
         {
           "environment":"test",
           "date":"2018-05-10T21:44:25.000Z",
           "deploy-signature":"24ecc7bac8a4d727d6a3a2350b6fda53812ee86f"
         },
         {
           "environment":"dev",
           "date":"2018-05-10T21:44:21.000Z",
           "deploy-signature":"503a335c99a190501456194d13ff722194e55613"
         }
      ]
   },
   "file-sync-client-status":{
      "all-synced":false,
      "file-sync-clients":{
         "chihuahua":{
            "last_check_in_time":null,
            "synced-with-file-sync-storage":false,
            "deployed":[
            ]
         },
         "localhost":{
            "last_check_in_time":"2018-05-11T22:41:20.270Z",
            "synced-with-file-sync-storage":true,
            "deployed":[
               {
                  "environment":"prod",
                  "date":"2018-05-11T22:40:48.000Z",
                  "deploy-signature":"66d620604c9465b464a3dac4884f96c43748b2c5"
               },
               {
                  "environment":"test",
                  "date":"2018-05-11T22:40:48.000Z",
                  "deploy-signature":"24ecc7bac8a4d727d6a3a2350b6fda53812ee86f"
               },
               {
                  "environment":"dev",
                  "date":"2018-05-11T22:40:50.000Z",
                  "deploy-signature":"503a335c99a190501456194d13ff722194e55613"
               }
            ]
         }
      }
   }
}
```

### Error responses

If code deployment fails, the  `"deploys-status"` section of the response provides an `"error"` key for the environment with the failure. The `"error"` key contains information about the failure.

Deployment failures can remain in the response even after the environment in question is successfully deployed. Old failures are removed from the `"deploys-status"` response during garbage collection.

|Key|Definition|Value|
|---|----------|-----|
|`"environment"`|The name of the environment queued or deployed.|A string specifying the name of an environment.|
|`"error"`|Information about the deployment failure.|Contains keys with error details.|
|`"corrected-env-name"`|The name of the environment.|A string specifying the name.|
|`"kind"`|The kind of error encountered.|An error type.|
|`"msg"`|The error message.|An error message.|
|`"queued-at"`|The date and time when the environment was queued.|A date and time stamp.|

For example, with a failure, the `"deploys-status"` response section might look something like:

```
[
    {
        "deploys-status": {
        "deploying": [],
        "failed": [
            {
                "environment": "test14",
                "error": {
                    "details": {
                        "corrected-env-name": "test14"
                    },
                    "kind": "puppetlabs.code-manager/deploy-failure",
                    "msg": "Errors while deploying environment 'test14' (exit code: 1):\nERROR\t -> Authentication failed for Git remote \"https://github.com/puppetlabs/puffppetlabs-apache\".\n"
                },
                "queued-at": "2018-06-01T21:28:18.292Z"
            }
        ],
        "new": [],
        "queued": []
    },
   ...
  }
]
```

