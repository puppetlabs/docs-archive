# Metrics endpoints

Puppet Server is capable of tracking advanced metrics to give you additional insight into its performance and health.

The HTTPS metrics endpoints are available on port 8140 of the master server:

```
curl -k https://<DNS NAME OF YOUR MASTER>:8140/status/v1/services?level=debug`
```

**Note:** These API endpoints are a tech preview. The metrics described here are returned only when passing the `level=debug` URL parameter, and the structure of the returned data might change in future versions.

These metrics fall into three categories:

-   JRuby metrics \(`/status/v1/services/pe-jruby-metrics`\)
-   HTTP route metrics \(`/status/v1/services/pe-master`\)
-   Catalog compilation profiler metrics \(`/status/v1/services/pe-puppet-profiler`\)

All of these metrics reflect data for the lifetime of the current Puppet Server process and reset whenever the service is restarted. Any time-related metrics report milliseconds unless otherwise noted.

Like the standard status endpoints, the metrics endpoints return machine-consumable information about running services. This JSON response includes the same keys returned by a standard status endpoint request \(see JSON endpoints\). Each endpoint also returns additional keys in an `experimental` section.

## GET /status/v1/services/pe-jruby-metrics

The `/status/v1/services/pe-jruby-metrics` endpoint returns JSON containing information about the JRuby pools from which Puppet Server fulfills agent requests.

You must query it at port 8140 and append the `level=debug` URL parameter.

### Query parameters

No parameters are supported. Defaults to using the `critical` status level.

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`

### Response keys

The metrics are returned in two subsections of the `experimental` section: `jruby-pool-lock-status` and `metrics`.

The response's `experimental/jruby-pool-lock-status` section contains the following keys:

|Key|Definition|
|---|----------|
|`current-state`|The state of the JRuby pool lock, which should be either `:not-in-use` \(unlocked\), `:requested` \(waiting for lock\), or `:acquired` \(locked\).|
|`last-change-time`|The date and time of the last `current-state` update, formatted as an ISO 8601 combined date and time in UTC.|

The response's `experimental/metrics` section contains the following keys:

|Key|Definition|
|---|----------|
|`average-borrow-time`|The average amount of time a JRuby instance spends handling requests, calculated by dividing the total duration in milliseconds of the `borrowed-instances` value by the `borrow-count` value.|
|`average-free-jrubies`|The average number of JRuby instances that are not in use over the Puppet Server process’s lifetime.|
|`average-lock-held-time`|The average time the JRuby pool held a lock, starting when the value of`jruby-pool-lock-status/current-state` changed to `:acquired`. This time mostly represents file sync syncing code into the live codedir, and is calculated by dividing the total length of time that Puppet Server held the lock by the value of `num-pool-locks`.|
|`average-lock-wait-time`|The average time Puppet Server spent waiting to lock the JRuby pool, starting when the value of `jruby-pool-lock-status/current-state` changed to`:requested`\). This time mostly represents how long Puppet Server takes to fulfill agent requests, and is calculated by dividing the total length of time that Puppet Server waits for locks by the value of `num-pool-locks`.|
|`average-requested-jrubies`|The average number of requests waiting on an available JRuby instance over the Puppet Server process’s lifetime.|
|`average-wait-time`|The average time Puppet Server spends waiting to reserve an instance from the JRuby pool, calculated by dividing the total duration in milliseconds of requested-instances by the requested-count value.|
|`borrow-count`|The total number of JRuby instances that have been used.|
|`borrow-retry-count`|The total number of times that a borrow attempt failed and was retried, such as when the JRuby pool is flushed while a borrow attempt is pending.|
|`borrow-timeout-count`|The number of requests that were not served because they timed out while waiting for a JRuby instance.|
|`borrowed-instances`|A list of the JRuby instances currently in use, each reporting: -   `duration-millis`: The length of time that the instance has been running.
-   `reason/request`: A hash of details about the request being served.

-   `request-method`: The HTTP request method, such as POST, GET, PUT, or DELETE.
-   `route-id`: The route being served. For routing metrics, see the HTTP metrics endpoint.

-   `uri`: The request’s full URI.

-   `time`: The time \(in milliseconds since the Unix epoch\) when the JRuby instance was borrowed.

|
|`num-free-jrubies`|The number of JRuby instances in the pool that are ready to be used.|
|`num-jrubies`|The total number of JRuby instances.|
|`num-pool-locks`|The total number of times the JRuby pools have been locked.|
|`requested-count`|The number of JRuby instances borrowed, waiting, or that have timed out.|
|`requested-instances`|A list of the requests waiting to be served, each reporting: -   `duration-millis`: The length of time the request has waited.

-   `reason/request`: A hash of details about the waiting request.

-   `request-method`: The HTTP request method, such as POST, GET, PUT, or DELETE.

-   `route-id`: The route being served. For routing metrics, see the HTTP metrics endpoint.

-   `uri`: The request’s full URI.

-   `time`:The time \(in milliseconds since the Unix epoch\) when Puppet Server received the request.


|
|`return-count`|The total number of JRuby instances that have been used.

|

For example:

```json
"pe-jruby-metrics": {
    "detail_level": "debug",
    "service_status_version": 1,
    "service_version": "2.2.22",
    "state": "running",
    "status": {
        "experimental": {
            "jruby-pool-lock-status": {
                "current-state": ":not-in-use",
                "last-change-time": "2015-12-03T18:59:12.157Z"
            },
            "metrics": {
                "average-borrow-time": 292,
                "average-free-jrubies": 0.4716243097301104,
                "average-lock-held-time": 1451,
                "average-lock-wait-time": 0,
                "average-requested-jrubies": 0.21324752542875958,
                "average-wait-time": 156,
                "borrow-count": 639,
                "borrow-retry-count": 0,
                "borrow-timeout-count": 0,
                "borrowed-instances": [
                    {
                        "duration-millis": 3972,
                        "reason": {
                            "request": {
                                "request-method": "post",
                                "route-id": "puppet-v3-catalog-/*/",
                                "uri": "/puppet/v3/catalog/hostname.example.com"
                            }
                        },
                        "time": 1448478371406
                    }
                ],
                "num-free-jrubies": 0,
                "num-jrubies": 1,
                "num-pool-locks": 2849,
                "requested-count": 640,
                "requested-instances": [
                    {
                        "duration-millis": 3663,
                        "reason": {
                            "request": {
                                "request-method": "put",
                                "route-id": "puppet-v3-report-/*/",
                                "uri": "/puppet/v3/report/hostname.example.com"
                            }
                        },
                        "time": 1448478371715
                    }
                ],
                "return-count": 638
            }
        }
    }
}
```

## GET /status/v1/services/pe-master

The `/status/v1/services/pe-master` endpoint returns JSON containing information about the routes that agents use to connect to this server.

You must query it at port 8140 and append the `level=debug` URL parameter.

### Query parameters

No parameters are supported. Defaults to using the `critical` status level.

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`

### Response keys

The response's `experimental/http-metrics` section contains a list of routes, each containing the following keys:

|Key|Definition|
|---|----------|
|`aggregate`|The total time Puppet Server spent processing requests for this route.|
|`count`|The total number of requests Puppet Server processed for this route.|
|`mean`|The average time Puppet Server spent on each request for this route, calculated by dividing the `aggregate` value by the `count`.|
|`route-id`|The route being served. The request returns a route with the special `route-id` of "total", which represents the aggregate data for all requests along all routes.|

Routes for newer versions of Puppet Enterprise and newer agents are prefixed with `puppet-v3`, while Puppet Enterprise 3 agents' routes are not. For example, a PE 2017.3 `route-id` might be `puppet-v3-report-/*/`, while the equivalent PE 3 agent's `route-id` is `:environment-report-/*/`.

For example:

```json
"pe-master": {
    {...},
    "status": {
        "experimental": {
            "http-metrics": [
                {
                    "aggregate": 70668,
                    "count": 234,
                    "mean": 302,
                    "route-id": "total"
                },
                {
                    "aggregate": 28613,
                    "count": 13,
                    "mean": 2201,
                    "route-id": "puppet-v3-catalog-/*/"
                },
                {...}
            ]
        }
    }
}
```

## GET /status/v1/services/pe-puppet-profiler

The `/status/v1/services/pe-puppet-profiler` endpoint returns JSON containing statistics about catalog compilation. You can use this data to discover which functions or resources are consuming the most resources or are most frequently used.

You must query it at port 8140 and append the `level=debug` URL parameter.

The Puppet Server profiler is enabled by default, but if it has been disabled, this endpoint's metrics are not available. Instead, the endpoint returns the same keys returned by a standard status endpoint request and an empty `status` key.

### Query parameters

No parameters are supported. Defaults to using the `critical` status level.

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`

### Response keys

If the profiler is enabled, the response returns two subsections in the `experimental` section:

-   `experimental/function-metrics`, containing statistics about functions evaluated by Puppet Server when compiling catalogs.
-   `experimental/resource-metrics`, containing statistics about resources declared in manifests compiled by Puppet Server.

Each function measured in the `function-metrics` section also has a **function** key containing the function's name, and each resource measured in the `resource-metrics` section has a **resource** key containing the resource's name.

The two sections otherwise share these keys:

|Key|Definition|
|---|----------|
|`aggregate`|The total time spent handling this function call or resource during catalog compilation.|
|`count`|The number of times Puppet Server has called the function or instantiated the resource during catalog compilation.|
|`mean`|The average time spent handling this function call or resource during catalog compilation, calculated by dividing the `aggregate` value by the `count`.|

For example:

```json
"pe-puppet-profiler": {
    {...},
    "status": {
        "experimental": {
            "function-metrics": [
                {
                    "aggregate": 1628,
                    "count": 407,
                    "function": "include",
                    "mean": 4
                },
                {...},
            "resource-metrics": [
                {
                    "aggregate": 3535,
                    "count": 5,
                    "mean": 707,
                    "resource": "Class[Puppet_enterprise::Profile::Console]"
                },
                {...},
            ]
        }
    }
}
```

