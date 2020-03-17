# The metrics API

Puppet Enterprise includes an optional, enabled-by-default web endpoint for Java Management Extension \(JMX\) metrics, namely managed beans \(MBeans\).

These endpoints include:

-   `GET /metrics/v1/mbeans`
-   `POST /metrics/v1/mbeans`
-   `GET /metrics/v1/mbeans/<name>`

**Note:** These API endpoints are a tech preview. The metrics described here are returned only when passing the `level=debug` URL parameter, and the structure of the returned data might change in future versions.To disable this endpoint, set `puppet_enterprise::master::puppetserver::metrics_webservice_enabled: false` in Hiera.

**Parent topic:**[Status API](status_api.md)

## GET /metrics/v1/mbeans

The `GET /metrics/v1/mbeans` endpoint lists available MBeans.

### Response keys

-   The key is the name of a valid MBean.
-   The value is a URI to use when requesting that MBean's attributes.

## POST /metrics/v1/mbeans

The `POST /metrics/v1/mbeans` endpoint retrieves requested MBean metrics.

### Query parameters

The query doesn't require any parameters, but the request body must contain a JSON object whose values are metric names, or a JSON array of metric names, or a JSON string containing a single metric's name.

For a list of metric names, make a `GET` request to `/metrics/v1/mbeans`.

### Response keys

The response format, though always JSON, depends on the request format:

-   Requests with a JSON object return a JSON object where the values of the original object are transformed into the Mbeans' attributes for the metric names.
-   Requests with a JSON array return a JSON array where the items of the original array are transformed into the Mbeans' attributes for the metric names.
-   Requests with a JSON string return the a JSON object of the Mbean's attributes for the given metric name.

## GET /metrics/v1/mbeans/<name\>

The `GET /metrics/v1/mbeans/<name>` endpoint reports on a single metric.

### Query parameters

The query doesn't require any parameters, but the endpoint itself must correspond to one of the metrics returned by a `GET` request to `/metrics/v1/mbeans`.

### Response keys

The endpoint's responses contain a JSON object mapping strings to values. The keys and values returned in the response vary based on the specified metric.

For example:

Use `curl` from localhost to request data on MBean memory usage:

```
curl 'http://localhost:8080/metrics/v1/mbeans/java.lang:type=Memory'
```

The response contains a JSON object representing the data:

```json
{
  "ObjectPendingFinalizationCount" : 0,
  "HeapMemoryUsage" : {
    "committed" : 807403520,
    "init" : 268435456,
    "max" : 3817865216,
    "used" : 129257096
  },
  "NonHeapMemoryUsage" : {
    "committed" : 85590016,
    "init" : 24576000,
    "max" : 184549376,
    "used" : 85364904
  },
  "Verbose" : false,
  "ObjectName" : "java.lang:type=Memory"
}
```

