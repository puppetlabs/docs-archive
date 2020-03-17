# Forming node classifier requests

Requests to the node classifier API must be well-formed HTTP\(S\) requests.

By default, the node classifier service listens on port 4433 and all endpoints are relative to the `/classifier-api/` path. For example, the full URL for the `/v1/groups` endpoint on localhost would be `https://localhost:4433/classifier-api/v1/groups`.

If needed, you can change the port the classifier API listens on.

**Parent topic:**[Node classifier API v1](node_classifier_service_api.md)

**Related information**  


[Configuring and tuning the console](config_console.md#)

## Authenticating to the node classifier API

You need to authenticate requests to the node classifier API. You can do this using RBAC authentication tokens or with the RBAC certificate whitelist.

### Authentication token

You can make requests to the node classifier API using RBAC authentication tokens.

For detailed information about authentication tokens, see token-based authentication.

In the this example, we are using the `/groups` endpoint of the node classifier API to get a list of all groups that exist in the node classifier, along with their associated metadata. The example assumes that you have already generated a token and saved it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

```
curl -k -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups -H "X-Authentication:$TOKEN"
```

The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you might need to supply the token in a token parameter. To supply the token in a token parameter, you would specify the request like this:

```
curl -k -X GET "https://<HOSTNAME>:<PORT>/classifier-api/v1/groups?token=$TOKEN
```

**Note:** Supplying the token as a token parameter is not as secure as using the X-Authentication method.

### Whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist. The RBAC whitelist is located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. If you edit this file, you must reload the `pe-console-services` service for your changes to take effect \(`sudo service pe-console-services reload`\). You can attach the certificate using the command line as demonstrated in the example cURL query below. You must have the whitelist certificate name and the private key to run the script.

The following query returns a list of all groups that exist in the node classifier, along with their associated metadata. This query shows how to attach the whitelist certificate to authenticate the node classifier API.

In this query, the "whitelisted certname" needs to match a name in the file, `/etc/puppetlabs/console-services/rbac-certificate-whitelist`.

```
curl -X GET https://<HOSTNAME>:<PORT>/classifier-api/v1/groups \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json"
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

**Related information**  


[Token-based authentication](rbac_token_auth_intro.md#)

## Using pagination parameters

If you have a large number of groups, classes, nodes, node check-ins, or environments, then sending a GET request through the classifier API could return an excessively large number of items.

To limit the number of items returned, you can use the `limit` and `offset` parameters.

-   `limit`: The value of the `limit` parameter limits how many items are returned in a response. The value must be a non-negative integer. If you specify a value other than a non-negative integer, you get a 400 Bad Request error.

-   `offset`: The value of the `offset` parameter specifies the number of item that are skipped. For example, if you specify an offset of 20 with a limit of 10, as shown in the example below, the first 20 items are skipped and you get back item 21 through to item 30. The value must be a non-negative integer. If you specify a value other than a non-negative integer, you get a 400 Bad Request error.


The following example shows a request using the `limit` and `offset` parameters.

```
curl https://<DNS NAME OF CONSOLE>:4433/classifier-api/v1/groups?limit=10&offset=20 \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem \
		-H "Content-Type: application/json"
```

