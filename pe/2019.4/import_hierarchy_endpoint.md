# Import hierarchy endpoint

Use the import hierarchy endpoint to delete all existing node groups from the node classifier service and replace them with the node groups in the body of the submitted request.

**Parent topic:**[Node classifier API v1](node_classifier_service_api.md)

## POST /v1/import-hierarchy

Delete *all* existing node groups from the node classifier service and replace them with the node groups in the body of the submitted request.

The request's body must contain an array of node groups that form a valid and complete node group hierarchy. Valid means that the hierarchy does not contain any cycles, and complete means that every node group in the hierarchy is reachable from the root.

### Request format

The request body must be a JSON array of node group objects as described in the `groups` endpoint documentation. All fields of the node group objects must be defined; no default values are supplied by the service.

Note that the output of the group collection endpoint, `/v1/groups`, is valid input for this endpoint.

### Response format

If the submitted node groups form a complete and valid hierarchy, and the replacement operation is successful, a `204 No Content` response with an empty body is returned.

### Error responses

If any of the node groups in the array are malformed, a 400 Bad Request response is returned. The response contains the usual JSON error payload. The `kind` key is "schema-violation"; the `msg` key contains a short description of the problems with the malformed node groups; and the `details` key contains an object with three keys:

-   `submitted`: an array of only the malformed node groups found in the submitted request.
-   `error`: an array of structured descriptions of how the node group at the corresponding index in the `submitted` array failed to meet the schema.
-   `schema`: the structured schema for node group objects.

If the hierarchy formed by the node groups contains a cycle, then a 422 Unprocessable Entity response is returned. The response contains the usual JSON error payload, where the `kind` key is "inheritance-cycle", the `msg` key contains the names of the node groups in the cycle, and the `details` key contains an array of the complete node group objects in the cycle.

If the hierarchy formed by the node groups contains node groups that are unreachable from the root, then a 422 Unprocessable Entity response is returned. The response contains the usual JSON error payload, where the `kind` key is "unreachable-groups", the `msg` lists the names of the unreachable node groups, and the `details` key contains an array of the unreachable node group objects.

**Related information**  


[Groups endpoint](groups_endpoint.md#)

[Node classifier errors](node_classifier_errors.md#)

