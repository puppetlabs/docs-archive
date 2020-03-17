---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Events endpoints

Use the `events` endpoints to retrieve activity service events.

**Parent topic:**[Activity service API](activity_api.md)

## GET /v1/events

Fetches activity service events. Web session authentication is required.

### Request format

The `/v1/events` endpoint supports filtering through query parameters.

-   **`service_id`**

    *required* The ID of the service: `classifier`, `code-manager`, `pe-console`, or `rbac`.

-   **`subject_type`**

    The subject who performed the activity: `users`.

-   **`subject_id`**

    Comma-separated list of IDs associated with the defined subject type.

-   **`object_type`**

    The object affected by the activity: `node`, `node_groups`, `users`, `user_groups`, `roles`, `environment`, or `directory_server_settings`.

-   **`object_id`**

    Comma-separated list of IDs associated with the defined object type.

-   **`offset`**

    Number of commits to skip before returning events.

-   **`limit`**

    Maximum number of events to return. Default is 1000 events.


### Examples

```
curl -k -X GET "https://web5.mydomain.edu:4433/activity-api/v1/events?service_id=classifier&subject_type=users&subject_id=6868e4af-2996-46c6-8e42-1ae873f8a0ba" -H "X-Authentication:$TOKEN"
```

```
curl -k -X GET "https://web5.mydomain.edu:4433/activity-api/v1/events?service_id=pe-console&object_type=node&object_id=emerald-1.platform9.mydomain.edu" -H "X-Authentication:$TOKEN"
```

### Response format

Responses are returned in a structured JSON format. Example return:

```
{
    "commits":[ {
        "object": {
            "id": "b55c209d-e68f-4096-9a2c-5ae52dd2500c", "name": "web_servers"
        },
        "subject": {
            "id": "6868e4af-2996-46c6-8e42-1ae873f8a0ba", "name": "kai.evens"
        },
        "timestamp": "2019-03-05T18:52:27Z", "events":[ {
            "message": "Deleted the \"web_servers\" group with id b55c209d-e68f-4096-9a2c-5ae52dd2500c"
        }]
    }], "offset": 0, "limit": 1, "total-rows": 5
}
```

```
{
    "commits":[ {
        "object": {
            "id": "emerald-1.platform9.mydomain.edu", "name": "emerald-1.platform9.mydomain.edu"
        },
        "subject": {
            "id": "d1f4919a-cf65-4c26-8832-8c4b5302b58b", "name": "steve"
        },
        "timestamp": "2019-05-02T22:50:16Z", "events":[ {
            "message": "Scheduled job 3 request for facter_task task on emerald-1.platform9.mydomain.edu run via orchestrator job 7,759"
        }]
    }], "offset": 0, "limit": 1, "total-rows": 2915
}
```

## GET /v1/events.csv

Fetches activity service events and returns in a flat CSV format. Token-based authentication is required.

### Request format

The `/v1/events.csv` endpoint supports the same parameters as `/v1/events`

### Examples

```
curl -k -X GET "https://web5.mydomain.edu:4433/activity-api/v1/events.csv?service_id=classifier&subject_type=users&subject_id=6868e4af-2996-46c6-8e42-1ae873f8a0ba" -H "X-Authentication:$TOKEN"
```

### Response format

Responses are returned in a flat CSV format. Example return:

```no-highlight
Submit Time,Subject Type,Subject Id,Subject Name,Object Type,Object Id,Object Name,Type,What,Description,Message
YYYY-MM-DD 18:52:27.76,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,delete,node_group,delete_node_group,"Deleted the ""web_servers"" group with id b55c209d-e68f-4096-9a2c-5ae52dd2500c"
YYYY-MM-DD 18:52:02.391,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,create,node_group,create_node_group,"Created the ""web_servers"" group with id b55c209d-e68f-4096-9a2c-5ae52dd2500c"
YYYY-MM-DD 18:52:02.391,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,edit,node_group_description,edit_node_group_description,"Changed the description to """""
YYYY-MM-DD 18:52:02.391,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,edit,node_group_environment,edit_node_group_environment,"Changed the environment to ""production"""
YYYY-MM-DD 18:52:02.391,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,edit,node_group_environment_override,edit_node_group_environment_override,Changed the environment override setting to false
YYYY-MM-DD 18:52:02.391,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,b55c209d-e68f-4096-9a2c-5ae52dd2500c,web_servers,edit,node_group_parent,edit_node_group_parent,Changed the parent to ec519937-8681-43d3-8b74-380d65736dba
YYYY-MM-DD 00:41:18.944,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,ec519937-8681-43d3-8b74-380d65736dba,PE Orchestrator,edit,node_group_class_parameter,delete_node_group_class_parameter_puppet_enterprise::profile::orchestrator_use_application_services,"Removed the ""use_application_services"" parameter from the ""puppet_enterprise::profile::orchestrator"" class"
YYYY-MM-DD 00:41:10.631,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,ec519937-8681-43d3-8b74-380d65736dba,PE Orchestrator,edit,node_group_class_parameter,add_node_group_class_parameter_puppet_enterprise::profile::orchestrator_use_application_services,"Added the ""use_application_services"" parameter to the ""puppet_enterprise::profile::orchestrator"" class"
YYYY-MM-DD 20:41:30.223,users,6868e4af-2996-46c6-8e42-1ae873f8a0ba,kai.evens,node_groups,46e34005-28e4-4009-bc48-4813221e9ffb,PE Agent,schedule_deploy,node_group,schedule_puppet_agent_on_node_group,Schedule puppet agent run on nodes in this group to be run at 2019-01-16T08:00:00Z
```

