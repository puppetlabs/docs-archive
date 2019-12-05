---
layout: default
title: "Activity service API"
canonical: "/pe/latest/rbac_activityapis.html"
---

The Puppet Enterprise (PE) activity service logs changes to role-based access control (RBAC) entities, such as users, directory groups, and user roles. This page lists the endpoints for the activity service v1 API and describes how to create well-formed requests.

> **Note:** In addition to the endpoints on this page, there are some endpoints that you can use to check the health of the activity service. These are available through the [Status API documentation](./status_api.html#the-status-api).

# Forming requests

This page provides general information about making well-formed HTTP(S) requests to the activity service v1 API. It assumes that you are using cURL to make the request.

## Port and path

By default, the activity service listens on port 4433 and all endpoints are relative to the `/activity-api/` path. So, for example, the full URL for the `/v1/events` endpoint on localhost is `https://localhost:4433/activity-api/v1/events`.

## Authentication

### Authentication token

You need to authenticate requests to the activity service's API. You can do this using user authentication tokens. For detailed information about authentication tokens, see [Token-based authentication](./rbac_token_auth.html).

The example below shows how to use a token in an API request. In this example, we are using the `/events` endpoint of the activity service API to get a list of events that have been logged by the activity service. The example assumes that you have already [generated a token](./rbac_token_auth.html#generating-a-token-using-the-api-endpoint) and saved it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

    curl -k -X GET https://<HOSTNAME>:<PORT>/activity-api/v1/events?service_id=classifier -H "X-Authentication:$TOKEN"

### Whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must restart the `pe-console-services` service for your changes to take effect (by running, for example, `sudo service pe-console-services restart`). You can attach the certificate using the command line as demonstrated in the example cURL query below. You must have the whitelist certificate name and the private key to run the script.

The following query returns a list of events logged with the activity service. This query shows how to attach the whitelist certificate to authenticate the activity service API.

In this query, the "whitelisted certname" needs to match a name in the file, `/etc/puppetlabs/console-services/rbac-certificate-whitelist`.

```
curl -X GET https://<HOSTNAME>:<PORT>/activity-api/v1/events?service_id=classifier \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

# Endpoints

## GET /v1/events

Fetches events in a structured JSON format. Supports filtering through query parameters. Web session authentication is required.

**Filters:**

* service_id [required]
* subject\_type [optional; required only when subject\_id is provided]
* subject\_id [optional; comma-separated list of subject\_ids]
* object\_type [optional; required only when object\_id is provided]
* object\_id [optional; comma-separated list of object\_ids]
* offset=[optional; skip `n` event commits]
* limit=[optional; return no more than `n` event commits; defaults to 1000]

**Example response body:**

GET /v1/events?service\_id=classifier&subject\_type=users&subject_id=dfgdfc145-545dfg54f-fdg45s5s

``` json
{"commits":
    [{"object":{"id":"415dfsvdf-dfgd45dfg-4dsfg54d", "name":"Default Node Group"},
      "subject":{"id":"dfgdfc145-545dfg54f-fdg45s5s", "name":"Kate Gleason"},
      "timestamp":"2014-06-24T04:00:00Z",
      "events":
    [{"message":"Create Node"},
     {"message":"Create Node Class"}]}],
 "total-rows":1}
```

GET /v1/events?service_id=classifier&object_type=node_groups&object_id=415dfsvdf-dfgd45dfg-4dsfg54d

``` json
{"commits":
    [{"object":{"id":"415dfsvdf-dfgd45dfg-4dsfg54d", "name":"Default Node Group"},
      "subject":{"id":"dfgdfc145-545dfg54f-fdg45s5s", "name":"Kate Gleason"},
      "timestamp":"2014-06-24T04:00:00Z",
      "events":
    [{"message":"Create Node"},
     {"message":"Create Node Class"}]}],
 "total-rows":1}
```

## GET /v1/events.csv

Fetches events in a flat CSV format. Supports filtering through query parameters. Web session authentication is required.

**Filters:**

* service\_id=[required]
* subject\_type [optional; required only when subject_id is provided]
* subject\_id [optional; comma-separated list of subject_ids]
* object\_type [optional; required only when object_id is provided]
* object\_id [optional; comma-separated list of object_ids]
* offset=[optional; skip `n` event commits]
* limit=[optional; return no more than 'n' event commits; defaults to 10000]

**Example response body:**

GET /v1/events.csv?service_id=classifier&subject_type=users&subject_id=kate

```
Submit Time,Subject Type,Subject Id,Subject Name,Object Type,Object Id,Object Name,Type,What,Description,Message
2014-07-17 13:08:09.985221,users,kate,Kate Gleason,node\_groups,2,Default Node Group,create,node,create\_node,Create Node
2014-07-17 13:08:09.985221,users,kate,Kate Gleason,node\_groups,2,Default Node Group,create,node\_class,create\_node\_class,Create Node Class
```
