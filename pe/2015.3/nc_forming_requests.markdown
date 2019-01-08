---
layout: default
title: "Forming Node Classifier Requests"
canonical: "/pe/latest/nc_forming_requests.html"
---

# Forming Requests for the Node Classifier v1 API

This page provides general information about making well-formed HTTP(S) requests to the node classifier v1 API.

## Port and Path

By default, the node classifier service listens on port 4433 and all endpoints are relative to the `/classifier-api/` path. So, for example, the full URL for the `/v1/groups` endpoint on localhost would be `https://localhost:4433/classifier-api/v1/groups`.

## Authentication

### Authentication Token
You need to authenticate requests to the Node Classifier's API. You can do this using user authentication tokens. For detailed information about authentication tokens, see [Token-Based Authentication](./rbac_token_auth.html). 

The example below shows how to use a token in an API request. In this example, we are using the `/groups` endpoint of the Node Classifier API to get a list of all groups that exist in the node classifier, along with their associated metadata. The example assumes that you have already [generated a token](./rbac_token_auth.html#generating-a-token) and saved it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

   `curl -k -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups -H “X-Authentication:$TOKEN”`
   
The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you may need to supply the token in a token parameter. To supply the token in a token parameter, you would specify the request as follows:

   `curl -k -X GET "https://<HOSTNAME>:<PORT>/classifier-api/v1/groups?token=$TOKEN` 
   
   **Note:** Supplying the token as a token parameter is not as secure as using the X-Authentication method.

### Whitelisted Certificate
You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must restart the `pe-console-services` service for your changes to take effect (`sudo service pe-console-services restart`). You can attach the certificate using the command line as demonstrated in the example curl query below. You must have the whitelist certificate name and the private key to run the script.

The following query will return a list of all groups that exist in the node classifier, along with their associated metadata. This query shows how to attach the whitelist certificate to authenticate the Node Classifier API.

In this query, the "whitelisted certname" needs to match a name in the file, `/etc/puppetlabs/console-services/rbac-certificate-whitelist`.

`curl -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json"`

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

## Content-Type Header

All PUT and POST requests with non-empty bodies should have the `Content-Type` header set to `application/json`.

