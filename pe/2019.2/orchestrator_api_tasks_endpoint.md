---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Puppet orchestrator API: tasks endpoint

Use the `/tasks` endpoint to view details about the tasks pre-installed by PE and those you've installed.

## GET /tasks

Lists all tasks in a given environment.

### Parameters

The request accepts this query parameter:

|Parameter|Definition|
|---------|----------|
|`environment`|Returns the tasks in the specified environment. If unspecified, defaults to `production`.|

### Response format

The response is a JSON object that lists each known task with a link to additional information, and uses these keys:

|Key|Definition|
|---|----------|
|`environment`|A map containing a `name` key specifying the environment's name and a `code_id` key indicating the code ID where the task is listed.|
|`items`|Contains an array of all known tasks.|
|`id`|An absolute URL where the task's details are listed.|
|`name`|The full name of the task.|

```
{
  "environment": {
    "name": "production",
    "code_id": "urn:puppet:code-id:1:a86da166c30f871823f9b2ea224796e834840676;production"
  },
  "items": [
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/tasks/package/install",
      "name": "package::install"
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/tasks/package/upgrade",
      "name": "package::upgrade"
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/tasks/exec/init",
      "name": "exec"
    }
  ]
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `environment` parameter is not a legal environment name, the server returns a 400 response.|
|`puppetlabs.orchestrator/unknown-environment`|If the specified `environment` doesn't exist, the server returns a 404 response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## GET /tasks/:module/:taskname

Returns data about a specified task, including metadata and file information. For the default task in a module, `:taskname`  is  `init`.

### Parameters

The request accepts this query parameter:

|Parameter|Definition|
|---------|----------|
|`environment`|Returns the tasks in the specified environment. If unspecified, defaults to `production`.|

### Response format

The response is a JSON object that includes information about the specified task, and uses these keys:

|Key|Definition|
|---|----------|
|`id`|An absolute URL where the task's details are listed.|
|`name`|The full name of the task.|
|`environment`|A map containing a `name` key specifying the environment's name and a `code_id` key indicating the code ID where the task is listed.|
|`metadata`|A map containing the contents of the `<task>.json` file.|
|`files`|An array of the files in the task.|
|`filename`|The base name of the file.|
|`uri`|A map containing `path` and `params` fields to construct a URL to download the file content. The client determines which host to download the file from.|
|`sha256`|The SHA-256 hash of the file content, in lowercase hexadecimal form.|
|`size_bytes`|The size of the file content in bytes.|

For example:

```
{
  "id": "https://orchestrator.example.com:8143/orchestrator/v1/tasks/package/install",
  "name": "package::install",
  "environment": {
    "name": "production",
    "code_id": "urn:puppet:code-id:1:a86da166c30f871823f9b2ea224796e834840676;production"
  },
  "metadata": {
    "description": "Install a package",
    "supports_noop": true,
    "input_method": "stdin",
    "parameters": {
      "name": {
        "description": "The package to install",
        "type": "String[1]"
      },
      "provider": {
        "description": "The provider to use to install the package",
        "type": "Optional[String[1]]"
      },
      "version": {
        "description": "The version of the package to install, defaults to latest",
        "type": "Optional[String[1]]"
      }
    }
  },
  "files": [
    {
      "filename": "install",
      "uri": {
        "path": "/package/tasks/install",
        "params": {
          "environment": "production"
        }
      },
      "sha256": "a9089b5b9720dca38a49db6f164cf8a053a7ea528711325da1c23de94672980f",
      "size_bytes": 693
    }
  ]
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `environment` parameter is not a legal environment name, or the module or taskname is invalid, the server returns a 400 response.|
|`puppetlabs.orchestrator/unknown-environment`|If the specified environment doesn't exist, the server returns a 404 response.|
|`puppetlabs.orchestrator/unknown-task`|If the specified task doesn't exist within the specified environment, the server returns a 404 response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

