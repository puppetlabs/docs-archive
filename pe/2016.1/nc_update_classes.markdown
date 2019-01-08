---
layout: default
title: "Update classes endpoint"
canonical: "/pe/latest/nc_update_classes.html"
---

## POST /v1/update-classes

Trigger the node classifier to update class and environment definitions from the Puppet master. This is the endpoint that's used when you refresh classes in the console.

> **Note**: If you don't use Code Manager *and* you changed the default value of the [`environment-class-cache-enabled` server setting](./config_puppetserver.html#setting-environment-class-cache-enabled), you must [manually delete the environment cache]({{puppetserver}}/admin-api/v1/environment-cache.html) before using this endpoint.


#### Query parameters

* `environment`: (optional) If provided, fetches classes for only the specified environment.

#### Example query

~~~ ruby
curl -X POST https://localhost:4433/classifier-api/v1/update-classes?environment=production
--cert <PATH TO CERT>
--key <PATH TO KEY>
--cacert <PATH TO PUPPET CA CERT>
~~~

#### Response

A successful update will return a 201 response with an empty body.

#### Error responses

If the Puppet master returns an unexpected status to the node classifier, the server will return a 500 Server Error response with the following keys:

* `kind`: "unexpected-response"
* `msg`: describes the error
* `details`: a JSON object, which has `url`, `status`, `headers`, and `body` keys describing the response the classifier received from the Puppet master
