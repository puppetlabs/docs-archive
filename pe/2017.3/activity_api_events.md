---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Events endpoints

## GET /v1/events

Fetches activity service events. Web session authentication is required.

### Request format

The `/v1/events` endpoint supports filtering through query parameters:

|Parameter|Value|
|---------|-----|
|`service_id`|The ID of the service. \(Required.\)|
|`subject_id`|Comma-separated list of subject IDs. \(Optional.\)|
|`subject_type`|\(Required only when `subject_id` is specified.\)|
|`object_id`|Comma-separated list of object IDs. \(Optional.\)|
|`object_type`|\(Required only when `object_id` is specified.\)|
|`offset`|Number of commits to skip. \(Optional.\)|
|`limit`|Maximum number of events to return. Defaults to 1000. \(Optional.\)|

Example request body:

```
GET /v1/events?service\_id=classifier&subject\_type=users&subject_id=dfgdfc145-545dfg54f-fdg45s5s
```

```
GET /v1/events?service_id=classifier&object_type=node_groups&object_id=415dfsvdf-dfgd45dfg-4dsfg54d
```

### Response format

Responses are returned in a structured JSON format. Example return:

```json
{"commits":
    [{"object":{"id":"415dfsvdf-dfgd45dfg-4dsfg54d", "name":"Default Node Group"},
      "subject":{"id":"dfgdfc145-545dfg54f-fdg45s5s", "name":"<SUBJECT NAME>"},
      "timestamp":"2014-06-24T04:00:00Z",
      "events":
    [{"message":"Create Node"},
     {"message":"Create Node Class"}]}],
 "total-rows":1}
```

## GET /v1/events.csv

Fetches activity service events and returns in a flat CSV format. Web session authentication is required.

### Request format

The `/v1/events.csv` endpoint supports filtering through query parameters:

|Parameter|Value|
|---------|-----|
|`service_id`|The ID of the service. \(Required.\)|
|`subject_id`|Comma-separated list of subject IDs. \(Optional.\)|
|`subject_type`|\(Required only when `subject_id` is specified.\)|
|`object_id`|Comma-separated list of object IDs. \(Optional.\)|
|`object_type`|\(Required only when `object_id` is specified.\)|
|`offset`|Number of commits to skip. \(Optional.\)|
|`limit`|Maximum number of events to return. Defaults to 1000. \(Optional.\)|

Example request body:

```
GET /v1/events.csv?service_id=classifier&subject_type=users&subject_id=kate
```

### Response format

Responses are returned in a flat CSV format. Example return:

```
Submit Time,Subject Type,Subject Id,Subject Name,Object Type,Object Id,Object Name,Type,What,Description,Message
2014-07-17 13:08:09.985221,users,<SUBJECT ID>,<SUBJECT NAME>,node\_groups,2,Default Node Group,create,node,create\_node,Create Node
2014-07-17 13:08:09.985221,users,<SUBJECT ID>,<SUBJECT NAME>,node\_groups,2,Default Node Group,create,node\_class,create\_node\_class,Create Node Class
```

