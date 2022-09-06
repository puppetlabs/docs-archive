# User group endpoints

Groups are used to assign roles to a group of users, which is vastly more efficient than managing roles for each user individually. The `groups` endpoints enable you to get lists of groups, and to add a new directory group.

**Remember:** Group membership is determined by the directory service hierarchy and as such, local users cannot be in directory groups.

## User group keys

The following keys are used with the RBAC v1 API's `groups` endpoints.

|Key|Explanation|Example|
|---|-----------|-------|
|`id`|A UUID string identifying the group.|`"c099d420-5557-11e4-916c-0800200c9a66"`|
|`login`|the identifier for the user group on the directory server.|`"poets"`|
|`display_name`|The group's name as a string.|`"Poets"`|
|`role_ids`|An array of role IDs indicating which roles should be inherited by the group's members. An empty array is valid. This is the only field that can be updated via RBAC; the rest are immutable or synced from the directory service.|`[3 6 5]`|
|`is_group`

`is_remote`

`is_superuser`

|These flags indicate that the group is a group.|`true`, `true`, `false`, respectively|
|`is_revoked`|Setting this flag to `true` currently does nothing for a group.|`true`/`false`|
|`user_ids`|An array of UUIDs indicating which users inherit roles from this group.|`["3a96d280-54c9-11e4-916c-0800200c9a66"]`|

## GET /groups

Fetches all groups. Supports filtering by ID through query parameters. Authentication is required.

### Request format

The following requests all the groups:

```
GET /groups
```

To request only some groups, add a comma-separated list of group IDs:

```
GET /groups?id=65a068a0-588a-11e4-8ed6-0800200c9a66,75370a30-588a-11e4-8ed6-0800200c9a66
```

### Response format

The response is a JSON object that lists the metadata for all requested groups.

For example:

```json
[{
  "id": "65a068a0-588a-11e4-8ed6-0800200c9a66",
  "login": "hamsters",
  "display_name": "Hamster club",
  "role_ids": [2, 3],
  "is_group" : true,
  "is_remote" : true,
  "is_superuser" : false,
  "user_ids": ["07d9c8e0-5887-11e4-8ed6-0800200c9a66"]}
},{
  "id": "75370a30-588a-11e4-8ed6-0800200c9a66",
  "login": "chinchilla",
  "display_name": "Chinchilla club",
  "role_ids": [2, 1],
  "is_group" : true,
  "is_remote" : true,
  "is_superuser" : false,
  "user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"]
},{
  "id": "ccdbde50-588a-11e4-8ed6-0800200c9a66",
  "login": "wombats",
  "display_name": "Wombat club",
  "role_ids": [2, 3],
  "is_group" : true,
  "is_remote" : true,
  "is_superuser" : false,
  "user_ids": []
}]
```

## GET /groups/<sid\>

Fetches a single group by its subject ID \(sid\). Authentication is required.

### Response format

The response contains an ID for the group and a list of `role_ids` the group is directly assigned to.

For directory groups, the response contains the display name, the login field, a list of `role_ids` directly assigned to the group, and `user_ids` containing IDs of the remote users that belong to that group.

For example:

```json
{"id": "65a068a0-588a-11e4-8ed6-0800200c9a66",
"login": "hamsters",
"display_name": "Hamster club",
"role_ids": [2, 3],
"is_group" : true,
"is_remote" : true,
"is_superuser" : false,
"user_ids": ["07d9c8e0-5887-11e4-8ed6-0800200c9a66"]}
```

### Error responses

If the user who submits the GET request has not successfully authenticated, a 401 Unauthorized response will be returned.

If the current user does not have the appropriate user permissions to request the group data, a 403 Forbidden response will be returned.

## POST /groups

Creates a new remote group and attaches to the new group any roles specified in its roles list. Authentication is required.

### Request format

Accepts a JSON body containing an entry for `login`, and an array of `role_ids` to assign to the group initially.

For example:

```json
{"login":"Augmentators",
"role_ids": [1,2,3]}
```

### Response format

If the create operation is successful, a 201 Created response with a location header that points to the new resource will be returned.

### Error responses

If the login for the group conflicts with an existing group login, a 409 Conflict response will be returned.

## PUT /groups/<sid\>

Replaces the group with the specified ID \(sid\) with a new group object. Authentication is required.

### Request format

Accepts an updated group object containing all the keys that were received from the API initially. The only updatable field is `role_ids`. An empty roles array indicates a desire to remove the group from all the roles it was directly assigned to. Any other changed values are ignored.

For example:

```json
{"id": "75370a30-588a-11e4-8ed6-0800200c9a66",
"login": "chinchilla",
"display_name": "Chinchillas",
**"role_ids": [2, 1],**
"is_group" : true,
"is_remote" : true,
"is_superuser" : false,
"user_ids": ["1cadd0e0-5887-11e4-8ed6-0800200c9a66","5c1ab4b0-588b-11e4-8ed6-0800200c9a66"]}
```

### Response format

If the operation is successful, a 200 OK response including a group object with updated roles will be returned.

## DELETE /groups/<sid\>

Deletes the user group with the specified ID \(sid\) from RBAC without making any changes to the directory service. Authentication required.

### Response format

If the user group is successfully deleted, a 204 No Content with an empty body will be returned.

### Error responses

If the current user does not have the `user_groups:delete` permission for this user group, a 403 Forbidden response will be returned.

If a group with the provided identifier does not exist, a 404 Not Found response will be returned.

