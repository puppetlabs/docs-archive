---
layout: default
title: "PE 2015.2 » Status » Monitoring PE Services"
subtitle: "Monitoring PE Services"
canonical: "/pe/latest/status_api.html"
---

# The Status API

The status endpoints allow you to check the health of PE components. You can check the overall health of console-services, as well as the health of the individual services within console-services (the [activity service](./rbac_activityapis.html), the [RBAC service](./rbac_serviceindex.html), and the [Node Classifier service](./nc_index.html)). 

The endpoints provide both coarse-grained health information in an overall healthy/error/unknown status field, and fine-grained information such as the availability of the database, the health of other required services, or the connectivity to the Puppet master. It can be useful in automated monitoring of your PE infrastructure, to remove unhealthy service instances from a load-balanced pool, checking configuration values, or
when troubleshooting problems in PE.

>**Note**: The Status API is included in Puppet Enterprise 2015.2 as a tech preview. Puppet Labs tech previews provide early access to new technology still under development. As such, you should only use them for evaluation purposes and not in production environments. You can find more information on tech previews on the [tech preview](http://puppetlabs.com/services/tech-preview) support scope page.

## Authenticating Requests

You need to authenticate requests to the Status API using a certificate listed in RBAC’s certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must restart the `pe-console-services` service for your changes to take effect (`sudo service pe-console-services restart`). You can attach the certificate using the command line as demonstrated in the example curl query in [Forming Requests](#forming-requests). You must have the whitelist certificate name and the private key to run the script.

The status endpoints can be served over HTTP, which does not require any authentication. This is disabled by default. To enable it, in the PE console UI, go to the `PE Console` node group, and in the `puppet_enterprise::profile::console` class, set the `console_services_plaintext_status_enabled` parameter to `true`.

## Forming Requests

The HTTPS status endpoints are available on the console services API server, which uses port 4433 by default.
The path prefix is `/status`, so, for example, the URL to get the statuses for all services as JSON would be `https://<DNS NAME OF YOUR CONSOLE HOST>:4433/status/v1/services`.

Here's how you would access that URL using curl:

~~~
curl https://<DNS NAME OF YOUR CONSOLE HOST>:4433/status/v1/services --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem
~~~

If enabled, the HTTP status endpoints are available on port 8123. To change this port, in the PE console UI, go to the `PE Console` node group, and in the `puppet_enterprise::profile::console` class, set the `console_services_plaintext_status_port` parameter to your desired port number.
The path prefix is `/status`, so for example, the URL to get the statuses for all services as JSON would be `http://<DNS NAME OF YOUR CONSOLE HOST>:8123/status/v1/services`.

Here's how you would access that URL using curl:

~~~
curl http://<DNS NAME OF YOUR CONSOLE HOST>:8123/status/v1/services
~~~

## JSON endpoints

These two endpoints provide machine-consumable information about running services. They are intended for scripting and integration with other services.

The content type for these endpoints is `application/json; charset=utf-8`.

**Response Keys**

* **service_version**: Package version of the jar containing a given service.
* **service_status_version**: Version of the status API the service is using to report status.
* **state**: One of _running_, _error_, or _unknown_
* **detail_level**: How thorough of a check to be run. One of _critical_, _debug_, _info_.
* **status**: String message with information about a service's status. Usually only relevant for _error_ and _unknown_ statuses.

### GET /status/v1/services

#### Query Parameters 

- `level` (Optional) The possible values are: `critical`, `debug`, `info`. Defaults to `info`.

Returns statuses for all services the status service knows about in a JSON data structure.

#### Response Codes

* 200 if and only if all services report a status of _running_
* 503 if any service's status is _unknown_ or _error_
* 400 if a level parameter is set but is invalid (ie not _critical_, _debug_, or _info_)

#### Example Response

     {"rbac-service": {service_version: "1.2.4"
                       service_status_version: 1
                       state: "running"
                       detail_level: "info"
                       status: "rbac-service status 1 :info"}

      "classifier-service": {service_version: "1.3.0"
                             service_status_version: 1
                             state: "running"
                             detail_level: "info"
                             status: "classifier-service status 1 :info"}}

### GET /status/v1/services/&lt;SERVICE NAME&gt;

#### Query Parameters 

- `level` The possible values are: `critical`, `debug`, `info`. Defaults to `info`.

Returns the status of the specified service, such as "rbac-service" or "classifier-service".

#### Response Codes

* 200 if the service reports a status of _running_
* 503 if the service reports a status of _unknown_ or _error_
* 404 if no service named &lt;SERVICE NAME&gt; is found
* 400 if a level parameter is set but is invalid (ie not _critical_, _debug_, or _info_)

#### Example Response

     {service_version: "1.2.4"
      service_status_version: 1
      state: "running"
      detail_level: "info"
      service_name: "rbac-service"
      status: "rbac-service status 1 :info"}

## Plaintext Endpoints

These two endpoints are designed for load balancers that don't support any kind of
JSON parsing or parameter setting. They return simple string bodies (either the
state of the service in question or a simple error message) and a status code
relevant to the status result.

The content type for these endpoints is `text/plain; charset=utf-8`.

### GET /status/v1/simple

Returns a status that reflects all services the status service knows about. It
decides on what status to report using the following logic:

* _running_ if and only if all services are _running_
* _error_ if any service reports _error_
* _unknown_ if any service reports _unknown_ and no services report _error_

#### Query Parameters 

No parameters are supported. Defaults to using the _critical_ status level.

#### Response Codes

* 200 if and only if all services report a status of _running_
* 503 if any service's status is _unknown_ or _error_

#### Possible Responses

* "running"
* "error"
* "unknown"

### GET /status/v1/simple/&lt;SERVICE NAME&gt;

Returns the plaintext status of the specified service, such as "rbac-service" or "classifier-service".

#### Query Parameters

No parameters are supported. Defaults to using the _critical_ status level.

#### Response Codes

* 200 if service is _running_
* 503 if service is _unknown_ or _error_
* 404 if requested service is not found

#### Possible Responses

* "running"
* "error"
* "unknown"
* "not found: &lt;SERVICE NAME&gt;"
