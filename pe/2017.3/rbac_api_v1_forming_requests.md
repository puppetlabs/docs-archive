# Forming RBAC API requests

Web session authentication is required to access the RBAC API. You can authenticate requests by using either user authentication tokens or whitelisted certificates.

By default, the RBAC service listens on port 4433. All endpoints are relative to the `/rbac-api/` path. So, for example, the full URL for the `/v1/users` endpoint on localhost is `https://localhost:4433/rbac-api/v1/users`.

## Authentication using tokens

To use a user authentication token in an RBAC API request, generate a token and save it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`. You can then include the token in your API request, as in the following example:

```
curl -k -X GET https://<HOSTNAME>:<PORT>/activity-api/v1/events?service_id=classifier -H "X-Authentication:$TOKEN"

```

The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you may need to supply the token in a token parameter. To supply the token in a token parameter, specify the request as follows:

```
curl -k -X GET https://<HOSTNAME>:<PORT>/rbac-api/v1/users/current?token=$TOKEN

```

CAUTION:

Be aware when using the token parameter method that the token parameter may be recorded in server access logs.

## Authentication using whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must reload the `pe-console-services` service \(run `sudo service pe-console-services reload`\) for your changes to take effect.

Attach the certificate using the command line, as demonstrated in the example curl query below. You must have the whitelisted certificate name \(which must match a name in the `/etc/puppetlabs/console-services/rbac-certificate-whitelist` file\) and the private key to run the script.

```
curl -X GET https://<HOSTNAME>:<PORT>/rbac-api/v1/users \
    --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
    --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
    --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json"
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

**Related topics**  


[Token-based authentication](rbac_token_auth_intro.md#)

## Content-type headers in the RBAC API

RBAC only accepts JSON payloads in PUT and POST requests.

If a payload is provided, it is important to specify that the content is in JSON format. Thus, all `PUT` and `POST` requests with non-empty bodies should have the `Content-Type` header set to `application/json`.

