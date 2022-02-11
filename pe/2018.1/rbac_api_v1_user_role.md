# User roles endpoints

By assigning roles to users, you can manage them in sets that are granted access permissions to various PE objects. This makes tracking user access more organized and easier to manage. The `roles` endpoints enable you to get lists of roles and create new roles.

## User role keys

The following keys are used with the RBAC v1 API’s roles endpoints.

|Key|Explanation|Example|
|---|-----------|-------|
|`id`|An integer identifying the role.|`18`|
|`display_name`|The role's name as a string.|`"Viewers"`|
|`description`|A string describing the role's function.|`"View-only permissions"`|
|`permissions`|An array containing permission objects that indicate what permissions a role grants. An empty array is valid. See Permission keys for more information.|`[]`|
|`user_ids`

`group_ids`

|An array of UUIDs indicating which users and groups are directly assigned to the role. An empty array is valid.|`["fc115750-555a-11e4-916c-0800200c9a66"]`|

## GET /roles

Fetches all roles with user and group ID lists and permission lists. Authentication is required.

### Response format

Returns a JSON object containing all roles with user and group ID lists and permission lists.

For example:

```json
[{"id": 123,
  "permissions": [{"object_type":"node_groups",
                   "action":"edit_rules",
                   "instance":"*"}, ...],
  "user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"],
  "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
  "display_name": "A role",
  "description": "Edit node group rules"},
...]
```

## GET /roles/<rid\>

Fetches a single role by its ID \(rid\). Authentication is required.

### Response format

Returns a 200 OK response with the role object with a full list of permissions and user and group IDs.

For example:

```json
{"id": 123,
 "permissions": [{"object_type":"node_groups",
                  "action":"edit_rules",
                  "instance":"*"}, ...],
 "user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"],
 "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
 "display_name": "A role",
 "description": "Edit node group rules"}
```

## POST /roles

Creates a role, and attaches to it the specified permissions and the specified users and groups. Authentication is required.

### Request format

Accepts a new role object. Any of the arrays can be empty and "description" can be null.

For example:

```json
{"permissions": [{"object_type":"node_groups",
                  "action":"edit_rules",
                  "instance":"*"}, ...],
 "user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"],
 "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
 "display_name": "A role",
 "description": "Edit node group rules"}
```

### Response format

Returns a 201 Created response with a location header pointing to the new resource.

### Error responses

Returns a 409 Conflict response if the role has a name that collides with an existing role.

## PUT /roles/<rid\>

Replaces a role at the specified ID \(rid\) with a new role object. Authentication is required.

### Request format

Accepts the modified role object.

For example:

```json
{"id": 123,
 "permissions": [{"object_type":"node_groups",
                  "action":"edit_rules",
                  "instance":"*"}, ...],
 "user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"],
 "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
 "display_name": "A role",
 "description": "Edit node group rules"}
```

### Response format

Returns a 200 OK response with the modified role object.

### Error responses

Returns a 409 Conflict response if the new role has a name that collides with an existing role.

## DELETE /roles/<rid\>

Deletes the role identified by the role ID \(rid\). Users with this role will immediately lose the role and all permissions granted by it, but their session will be otherwise unaffected. Access to the next request that the user makes will be determined by the new set of permissions the user has without this role.

### Response format

Returns a 200 OK response if the role identified by `<rid>` has been deleted.

### Error responses

Returns a 404 Not Found response if no role exists for `<rid>`.

Returns a 403 Forbidden response if the current user lacks permission to delete the role identified by `<rid>`.

