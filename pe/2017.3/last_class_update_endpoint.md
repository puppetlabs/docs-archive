# Last class update endpoint

Use the last class update endpoint to retrieve the time that classes were last updated from the Puppet master.

## GET /v1/last-class-update

Use the `/v1/last-class-update` endpoint to retrieve the time that classes were last updated from the Puppet master.

### Response

The response will always be an object with one field, `last_update`. If there has been an update, the value of `last_update` field will be the time of the last update in ISO8601 format. If the node classifier has never updated from Puppet, the field will be null.

