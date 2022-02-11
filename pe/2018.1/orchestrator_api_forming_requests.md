# Puppet orchestrator API: forming requests

Instructions on interacting with this API.

By default, the orchestrator service listens on port `8143`, and all endpoints are relative to the `/orchestrator/v1` path. So, for example, the full URL for the `jobs` endpoint on localhost would be `https://localhost:8143/orchestrator/v1/jobs`.

**Tip:** Requests to the orchestrator API should be well-formed HTTP\(S\) requests.

## Authenticating to the orchestrator API with a token

You need to authenticate requests to the orchestrators’s API. You can do this using user authentication tokens.

For detailed information about authentication tokens, see [Token-based authentication](rbac_token_auth_intro.md#). The following example shows how to use a token in an API request.

To use the `jobs` endpoint of the orchestrator API to get a list of all jobs that exist in the orchestrator, along with their associated metadata, you'd first generate a token with the puppet-access tool. You'd then copy that token and replace `<TOKEN>` with that string in the following request:

```
curl -k -X GET https://<HOSTNAME>:<PORT>/orchestrator/v1/jobs -H 'X-Authentication:<TOKEN>'
```

## Example token usage: deploy an environment

If you want to deploy an environment with the orchestrator's API, you can form a request with the token you generated earlier. For example:

```
curl -k -H 'X-Authentication:<TOKEN>' https://<HOSTNAME>:<PORT>/orchestrator/v1/command/deploy -X POST -d '{"environment":"production"}' -H "Content-Type: application/json"
```

This returns a JSON structure that includes a link to the new job started by the orchestrator.

```json
{
  "job" : {
    "id" : "https://orchestrator.vm:8143/orchestrator/v1/jobs/81",
    "name" : "81"
  }
}
```

You can make an additional request to get more information about the job. For example:

```
curl -k -X GET https://<HOSTNAME>:<PORT>/orchestrator/v1/jobs/81 -H 'X-Authentication:<TOKEN>'
```

