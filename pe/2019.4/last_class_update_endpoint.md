# Last class update endpoint

Use the last class update endpoint to retrieve the time that classes were last updated from the Puppet master.

**Parent topic:**[Node classifier API v1](node_classifier_service_api.md)

## GET /v1/last-class-update

Use the `/v1/last-class-update` endpoint to retrieve the time that classes were last updated from the Puppet master.

### Response

The response is always an object with one field, `last_update`. If there has been an update, the value of `last_update` field is the time of the last update in ISO8601 format. If the node classifier has never updated from Puppet, the field is null.

