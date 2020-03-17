# Activity service plaintext endpoints

The activity service plaintext endpoints are designed for load balancers that don't support any kind of JSON parsing or parameter setting. They return simple string bodies \(either the state of the service in question or a simple error message\) and a status code relevant to the status result.

**Parent topic:**[Status API](status_api.md)

## GET /status/v1/simple

The `/status/v1/simple` returns a status that reflects all services the status service knows about.

The content type for this endpoint is `text/plain; charset=utf-8`.

### Query parameters

No parameters are supported. Defaults to using the `critical` status level.

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`

### Possible responses

The endpoint returns a status that reflects all services it knows about. It decides on what status to report using the following logic:

-   `running` if and only if all services are running

-   `error` if any service reports an error

-   `unknown` if any service reports an unknown and no services report an error


## GET /status/v1/simple/<SERVICE NAME\>

The `/status/v1/simple/<SERVICE NAME>` endpoint returns the plaintext status of the specified service, such as `rbac-service` or `classifier-service`.

The content type for this endpoints is `text/plain; charset=utf-8`.

### Query parameters

No parameters are supported. Defaults to using the `critical` status level.

### Response codes

The server uses the following response codes:

-   200 if and only if all services report a status of `running`
-   503 if any service’s status is `unknown` or `error`
-   404 if no service named <SERVICE NAME\> is found


### Possible responses

The endpoint returns a status that reflects all services it knows about. It decides on what status to report using the following logic:

-   `running` if and only if all services are running

-   `error` if any service reports an error

-   `unknown` if any service reports an unknown and no services report an error


-   `not found: <SERVICENAME>` if any service can't be found

