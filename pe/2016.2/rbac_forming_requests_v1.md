---
layout: default
title: "Forming RBAC requests: API v1"
canonical: "/pe/latest/rbac_forming_requests_v1.html"
---

This page provides general information about making well-formed HTTP(S) requests to the Role-based access control (RBAC) v1 API.

## Port and path

To make well-formed HTTP(S) requests to the RBAC v1 API, use the following port and path:

    https://localhost:4433/rbac-api/v1

In the request path, `localhost:4433` are the machine and port if you're making the call from the console machine. If you're not making the call from the console machine, then use the console machine's hostname in place of `localhost`.

For example:

    GET https://localhost:4433/rbac-api/v1/users

## Authentication

### Authentication token
You need to authenticate requests to the RBAC API. You can do this using user authentication tokens. For detailed information about authentication tokens, see [token-based authentication](./rbac_token_auth.html).

The example below shows how to use a token in an API request. In this example, we are using the `/users/current` endpoint of the RBAC API to get information about the current authenticated user. The example assumes that you have already [generated a token](./rbac_token_auth.html#generating-a-token-using-the-api-endpoint) and saved it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

    curl -k -X GET https://<HOSTNAME>:<PORT>/rbac-api/v1/users/current -H "X-Authentication:$TOKEN"

The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you may need to supply the token in a token parameter. To supply the token in a token parameter, specify the request as follows:

    curl -k -X GET https://<HOSTNAME>:<PORT>/rbac-api/v1/users/current?token=$TOKEN

> **Note:** Be aware when using the token parameter method that the token parameter may be recorded in server access logs.

### Whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must restart the `pe-console-services` service for your changes to take effect. You can attach the certificate using the command line as demonstrated in the example cURL query below. You must have the whitelist certificate name and the private key to run the script.

The following query returns a list of all users that exist in the RBAC service, along with their associated metadata. This query shows how to attach the whitelist certificate to authenticate the RBAC API.

In this query, the "whitelisted certname" needs to match a name in the file, `/etc/puppetlabs/console-services/rbac-certificate-whitelist`.

```
curl -X GET https://<HOSTNAME>:<PORT>/rbac-api/v1/users \
    --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
    --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
    --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json"
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

## Content-type header

RBAC only accepts JSON payloads in PUT and POST requests. If a payload is provided, it is important to specify that the content is in JSON format. Thus, all `PUT` and `POST` requests with non-empty bodies should have the `Content-Type` header set to `application/json`.
