# Console and console services known issues

These are the known issues for the console and console services in this release.

## Deactivated nodes shown in Packages page summary counts

If all the nodes on which a certain package is installed are deactivated, but the nodes' specified `node-purge-ttl`period has not yet elapsed, instances of the package still appear in summary counts on the Packages page. To correct this issue, adjust the `node-purge-ttl` setting and run garbage collection.

## Reports page summary counts might be inaccurate on first usage

Report count caching is used to improve console performance. In some cases, caching might cause summary counts of available reports to be displayed inaccurately the first time the page is accessed after a fresh install.

## Updated modes are shown without leading zero in events

When the `mode` attribute for a `file` resource is updated, and numeric notation is used, leading zeros are omitted in the **New Value** field on the **Events** page. For example, `0660` is shown as `660` and `0000` is shown as `0`.

## Performance issues with the `unpin-from-all` endpoint

As number of groups increases, response time can increase significantly with the `unpin-from-all` endpoint.

## Agent time skew results in incorrect error message

If an agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`.

