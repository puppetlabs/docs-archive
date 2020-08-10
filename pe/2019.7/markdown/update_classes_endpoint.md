# Update classes endpoint

Use update classes endpoint to trigger the node classifier to update class and environment definitions from the Puppet master.

## POST /v1/update-classes

Use the `/v1/update-classes` endpoint to trigger the node classifier to update class and environment definitions from the Puppet master. The classifier service uses this endpoint when you refresh classes in the console.

**Note:** If you don't use Code Manager *and* you changed the default value of the `environment-class-cache-enabled` server setting, you must [manually delete the environment cache](https://puppet.com/docs/puppetserver/latest/admin-api/v1/environment-cache.html) before using this endpoint.

### Query parameters

The request accepts the following optional parameter:

|Parameter|Value|
|---------|-----|
|`environment`|If provided, fetches classes for only the specified environment.|

For example:

```ruby
curl -X POST https://localhost:4433/classifier-api/v1/update-classes?environment=production
--cert <PATH TO CERT>
--key <PATH TO KEY>
--cacert <PATH TO PUPPET CA CERT>
```

### Response

For a successful update, the service returns a `201` response with an empty body.

### Error responses

If the Puppet master returns an unexpected status to the node classifier, the service returns a `500: Server Error` response with the following keys:

|Key|Definition|
|---|----------|
|`kind`|"unexpected-response"|
|`msg`|Describes the error|
|`details`|A JSON object, which has `url`, `status`, `headers`, and `body` keys describing the response the classifier received from the Puppet master|

**Related information**  


[Enable or disable cached data when updating classes](config_puppetserver.md#)

