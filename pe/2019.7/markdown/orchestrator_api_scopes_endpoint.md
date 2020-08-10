# Puppet orchestrator API: scopes endpoint

Use the `scopes` endpoint to retrieve resources for task constraints.

## GET /scopes/task\_targets

List all the known task targets \(set of tasks + set of nodes + set of node groups\) stored in orchestrator.

### Response format

The response is a JSON object that lists the known task targets and uses the following keys:

|Key|Definition|
|---|----------|
|`items`|Contains an array of all the known task targets.|

The following keys are included under `items`:

|Key|Definition|
|---|----------|
|`id`|An absolute URL to retrieve the individual task target.|
|`name`|A string that is a unique identifier for the task target.|
|`display_name`|An optional string value that can be used to describe the task target.|
|`tasks`|An optional array of tasks that this task target corresponds to.|
|`all_tasks`|An optional boolean to determine if all tasks can be run on designated targets.|
|`nodes`|An array of certnames that this task target corresponds to. The array can be empty.|
|`node_groups`|An array of node group ids that this task target corresponds to. The array can be empty.|
|`pql_query`|\(optional\) A string that is a single PQL query that the task target corresponds to.|

For example:

```
{
 "items": [
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/scopes/task_targets/1",
      "name": "1",
      "tasks": [
        "package::install",
        "exec"
      ],
      "all_tasks": "false",
      "nodes": [
        "wss6c3w9wngpycg",
        "jjj2h5w8gpycgwn"
      ],
      "node_groups":[
        "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
        "4932bfe7-69c4-412f-b15c-ac0a7c2883f1"
      ],
      "pql_query": "nodes[certname] { catalog_environment = \"production\" }"
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/scopes/task_targets/2",
      "name": "2",
      "tasks": [
        "imaginary::task"
      ],
      "all_tasks": "false",
      "nodes": [
        "mynode"
      ],
      "node_groups":[
      ]
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/scopes/task_targets/3",
      "name": "3",
      "all_tasks": true,
      "nodes": [
        "xxx6c3w9wngpycg",
        "bbb2h5w8gpycgwn"
      ],
      "node_groups":[
        "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
        "4932bfe7-69c4-412f-b15c-ac0a7c2883f1"
      ]
    }
  ]
}
```

## GET /scopes/task\_targets/:id

Get information about a specific task\_target.

### Response format

The response is a JSON object that provides the details about the task\_target and uses the following keys:

|Keys|Definitions|
|----|-----------|
|`id`|An absolute URL to retrieve the individual task\_target.|
|`name`|A string that is a unique identifier for the task\_target.|
|`display_name`|An optional string value that can be used to describe the task\_target.|
|`tasks`|An optional array of tasks that this task\_target corresponds to.|
|`all_tasks`|An optional boolean to determine if all tasks can be run on designated targets.|
|`nodes`|An array of certnames that this task\_target corresponds to. The array can be empty.|
|`node_groups`|An array of node group ids that this task\_target corresponds to. The array can be empty.|
|`pql_query`|\(optional\) A string that is a single PQL query that the task\_target corresponds to.|

For example:

```
{
  "id": "https://orchestrator.example.com:8143/orchestrator/v1/scopes/task_targets/1",
  "name": "1",
  "tasks": [
    "package::install",
    "exec"
  ],
  "all_tasks": "false",
  "nodes": [
    "wss6c3w9wngpycg",
    "jjj2h5w8gpycgwn"
  ],
  "node_groups":[
    "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
    "4932bfe7-69c4-412f-b15c-ac0a7c2883f1"
  ],
  "pql_query": "nodes[certname] { catalog_environment = \"production\" }"
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict. See the [Puppet orchestrator API: error responses](orchestrator_api_error_responses.md) documentation for the general format of error responses.

|Key|Definition|
|---|----------|
|`:puppetlabs.orchestrator/unknown-task-target`|If the specified `id` doesn't exist, the server returns a `404` response.|

