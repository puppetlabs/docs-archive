---
layout: default
title: "Forming node classifier requests"
canonical: "/pe/latest/nc_forming_requests.html"
---

# Forming requests for the node classifier v1 API

This page provides general information about making well-formed HTTP(S) requests to the node classifier v1 API.

## Port and path

By default, the node classifier service listens on port 4433 and all endpoints are relative to the `/classifier-api/` path. So, for example, the full URL for the `/v1/groups` endpoint on localhost would be `https://localhost:4433/classifier-api/v1/groups`.

If needed, you can [change the port the classifier API listens on](./console_config.html#changing-the-consoles-api-port).  

## Authentication

### Authentication token

You need to authenticate requests to the Node Classifier's API. You can do this using user authentication tokens. For detailed information about authentication tokens, see [token-based authentication](./rbac_token_auth.html).

The example below shows how to use a token in an API request. In this example, we are using the `/groups` endpoint of the Node Classifier API to get a list of all groups that exist in the node classifier, along with their associated metadata. The example assumes that you have already [generated a token](./rbac_token_auth.html#generating-tokens-with-the-api-endpoint) and saved it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

    curl -k -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups -H "X-Authentication:$TOKEN"

The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you may need to supply the token in a token parameter. To supply the token in a token parameter, you would specify the request as follows:

    curl -k -X GET "https://<HOSTNAME>:<PORT>/classifier-api/v1/groups?token=$TOKEN

> **Note:** Supplying the token as a token parameter is not as secure as using the X-Authentication method.

### Whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must reload the `pe-console-services` service for your changes to take effect (`sudo service pe-console-services reload`). You can attach the certificate using the command line as demonstrated in the example cURL query below. You must have the whitelist certificate name and the private key to run the script.

The following query will return a list of all groups that exist in the node classifier, along with their associated metadata. This query shows how to attach the whitelist certificate to authenticate the Node Classifier API.

In this query, the "whitelisted certname" needs to match a name in the file, `/etc/puppetlabs/console-services/rbac-certificate-whitelist`.

```
curl -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json"
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

## Content-type header

All PUT and POST requests with non-empty bodies should have the `Content-Type` header set to `application/json`.

## Pagination parameters

If you have a large number of groups, classes, nodes, node check-ins, or environments, then sending a GET request through the classifier API could return an excessively large number of items. To limit the number of items returned, you can use the `limit` and `offset` parameters.

* `limit`: The value of the `limit` parameter limits how many items are returned in a response. The value must be a non-negative integer. If you specify a value other than a non-negative integer, you will get a 400 Bad Request error.

* `offset`: The value of the `offset` parameter specifies the number of item that are skipped. For example, if you specify an offset of 20 with a limit of 10, as shown in the example below, the first 20 items are skipped and you get back item 21 through to item 30. The value must be a non-negative integer. If you specify a value other than a non-negative integer, you will get a 400 Bad Request error.

#### Example pagination parameters:

```
curl https://<DNS NAME OF CONSOLE>:4433/classifier-api/v1/groups?limit=10&offset=20 \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem \
		-H "Content-Type: application/json"
```