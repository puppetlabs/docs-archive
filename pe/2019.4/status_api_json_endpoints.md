---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# JSON endpoints

These two endpoints provide machine-consumable information about running services. They are intended for scripting and integration with other services.

**Parent topic:**[Status API](status_api.md)

## GET /status/v1/services

Use the `/services` endpoint to retrieve the statuses of all PE services.

The content type for this endpoint is `application/json; charset=utf-8`.

### Query parameters

The request accepts the following parameters:

|Parameter|Value|
|---------|-----|
|`level`|How thorough of a check to run. Set to `critical`, `debug`, or `info` \(default\).|
|`timeout`|Specified in seconds; defaults to `30`.|

### Response format

The response is a JSON object that lists details about the services, using the following keys:

|Key|Definition|
|---|----------|
|`service_version`|Package version of the JAR file containing a given service.|
|`service_status_version`|The version of the API used to report the status of the service.|
|`detail_level`|Can be `critical`, `debug`, or `info`.|
|`state`|Can be `running`, `error`, or `unknown`.|
|`status`|An object with the service’s status details. Usually only relevant for error and unknown states.|
|`active_alerts`|An array of objects containing `severity` and a `message` about your replication from pglogical if you have replication enabled; otherwise, it's an empty array.|

For example:

```json
{"rbac-service": {"service_version": "1.8.11-SNAPSHOT",
                  "service_status_version": 1,
                  "detail_level": "info",
                  "state": "running",
                  "status": {
                    "activity_up": true,
                    "db_up": true,
                    "db_pool": { "state": "ready" },
                    "replication": { "mode": "none", "status": "none" }
                  },
                  "active_alerts": [],
                  "service_name": "rbac-service"
                  }


 "classifier-service": {"service_version": "1.8.11-SNAPSHOT",
                  "service_status_version": 1,
                  "detail_level": "info",
                  "state": "running",
                  "status": {
                    "activity_up": true,
                    "db_up": true,
                    "db_pool": { "state": "ready" },
                    "replication": { "mode": "none", "status": "none" }
                  },
                  "active_alerts": [],
                  "service_name": "classifier-service"
                  }
```

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`
-   400 if a level parameter is set but is invalid \(not `critical`, `debug`, or `info`\)

## GET /status/v1/services/<SERVICE NAME\>

Use the `/services/<SERVICE NAME>` endpoint to retrieve the status of a particular PE service.

The content type for this endpoint is `application/json; charset=utf-8`.

### Query parameters

The request accepts the following parameters:

|Parameter|Value|
|---------|-----|
|`level`|How thorough of a check to run. Set to `critical`, `debug`, or `info` \(default\).|
|`timeout`|Specified in seconds; defaults to `30`.|

### Response format

The response is a JSON object that lists details about the service, using the following keys:

|Key|Definition|
|---|----------|
|`service_version`|Package version of the JAR file containing a given service.|
|`service_status_version`|The version of the API used to report the status of the service.|
|`detail_level`|Can be `critical`, `debug`, or `info`.|
|`state`|Can be `running`, `error`, or `unknown`.|
|`status`|An object with the service’s status details. Usually only relevant for error and unknown states.|
|`active_alerts`|An array of objects containing `severity` and a `message` about your replication from pglogical if you have replication enabled; otherwise, it's an empty array.|

For example:

```json
{"rbac-service": {"service_version": "1.8.11-SNAPSHOT",
                  "service_status_version": 1,
                  "detail_level": "info",
                  "state": "running",
                  "status": {
                    "activity_up": true,
                    "db_up": true,
                    "db_pool": { "state": "ready" },
                    "replication": { "mode": "none", "status": "none" }
                  },
                  "active_alerts": [],
                  }
```

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`
-   400 if a level parameter is set but is invalid \(not `critical`, `debug`, or `info`\)
-   404 if no service named `<SERVICE NAME>` is found


