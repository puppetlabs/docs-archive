---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Commands endpoint

Use the commands endpoint to unpin specified nodes from all groups they’re pinned to.

## POST /v1/commands/unpin-from-all

Use the /v1/commands/unpin-from-all to unpin specified nodes from all groups they’re pinned to. Nodes that are dynamically classified using rules aren't affected.

Nodes are unpinned from only those groups for which you have view and edit permissions. Because group permissions are applied hierarchically, you must have Create, edit, and delete child groups or Edit child group rules permissions for the parent groups of the groups you want to unpin the node from.

### Request format

The request body must be a JSON object describing the nodes to unpin, using the following key:

|Key|Definition|
|---|----------|
|`nodes`|The certname of the nodes \(required\).|

For example:

```
{"nodes": ["foo", "bar"]}
```

### Response format

If unpinning is successful, the service returns a list of nodes with the groups they were unpinned from. If a node wasn't pinned to any groups, it's not included in the response.

```
{"nodes": [{"name": "foo",
            "groups": [{"id": "8310b045-c244-4008-88d0-b49573c84d2d",
                        "name": "Webservers",
                        "environment": "production"},
                       {"id": "84b19b51-6db5-4897-9409-a4a3a94b7f09",
                        "name": "Test",
                        "environment": "test"}]},
           {"name": "bar",
            "groups": [{"id": "84b19b51-6db5-4897-9409-a4a3a94b7f09",
                        "name": "Test",
                        "environment": "test"}]}]}
```

