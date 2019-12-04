# Validation endpoints

Use validation endpoints to validate groups in the node classifier.

## POST /v1/validate/group

Use the `/v1/validate/group` endpoint to validate groups in the node classifier.

### Request format

The request should contain a group object. The request uses the following keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(required\).|
|`environment`|The name of the node group's environment. This key is optional; if it's not provided, the default environment \(`production`\) will be used.|
|`environment_trumps`|Whether this node group's environment should override those of other node groups at classification-time. This key is optional; if it's not provided, the default value of `false` will be used.|
|`description`|A string describing the node group. This key is optional; if it's not provided, the node group will have no description and this key will not appear in responses.|
|`parent`|The ID of the node group's parent \(required\).|
|`rule`|The condition that must be satisfied for a node to be classified into this node group. The structure of this condition is described in the "Rule Condition Grammar" section above.|
|`variables`|An object that defines the names and values of any top-level variables set by the node group. The keys of the object are the variable names, and the corresponding value is that variable's value, which can be any sort of JSON value. The `variables` key is optional, and if a node group does not define any top-level variables then it may be omitted.|
|`classes`|An object that defines the classes to be used by nodes in the node group. The `classes` key is required, and at minimum should be an empty object \(\{\}\). The `classes` key also contains the parameters for each class. Some classes have required parameters. This is a two-level object; that is, the keys of the object are class names \(strings\), and each key's value is another object that defines class parameter values. This innermost object maps between class parameter names and their values. The keys are the parameter names \(strings\), and each value is the parameter's value, which can be any kind of JSON value. The `classes` key is not optional; if it is missing, the service returns a `400: Bad Request` response.|

### Response format

If the group is valid, the service returns a `200 OK` response with the validated group as the body.

If a validation error is encountered, the service returns one of the following `400`-level error responses.

Responses and keys returned for create group requests depend on the type of error.

`schema-violation`

If any of the required keys are missing or the values of any of the defined keys do not match the required type, the service returns a `400: Bad Request` response using the following keys:

|Key|Definition|
|---|----------|
|`kind`|"schema-violation"|
|`details`|An object that contains three keys: -   code\>submitted</code\>: Describes the submitted object.
-   `schema`: Describes the schema that object should conform to.
-   `error`: Describes how the submitted object failed to conform to the schema.

|

`malformed-request`

If the request's body could not be parsed as JSON, the service returns a `400: Bad Request` response using the following keys:

|Key|Definition|
|---|----------|
|`kind`|"malformed-request"|
|`details`|An object that contains two keys: -   `body`: Holds the request body that was received.
-   `error`: Describes how the submitted object failed to conform to the schema.

|

`uniqueness-violation`

If your attempt to create the node group violates uniqueness constraints \(such as the constraint that each node group name must be unique within its environment\), the service returns a `422: Unprocessable Entity` response using the following keys:

|Key|Definition|
|---|----------|
|`kind`|"uniqueness-violation"|
|`msg`|Describes which fields of the node group caused the constraint to be violated, along with their values.|
|`details`|An object that contains two keys: -   `conflict`: An object whose keys are the fields of the node group that violated the constraint and whose values are the corresponding field values.
-   `constraintName`: The name of the database constraint that was violated.

|

`missing-referents`

If classes or class parameters defined by the node group, or inherited by the node group from its parent, do not exist in the submitted node group's environment, the service returns a `422: Unprocessable Entity` response. In both cases the response object uses the following keys:

|Key|Definition|
|---|----------|
|`kind`|"missing-referents"|
|`msg`|Describes the error and lists the missing classes and/or parameters.|
|`details`|An array of objects, where each object describes a single missing referent, and has the following keys: -   `kind`: "missing-class" or "missing-parameter", depending on whether the entire class doesn't exist, or the class just doesn't have the parameter.
-   `missing`: The name of the missing class or class parameter.
-   `environment`: The environment that the class or parameter is missing from; i.e. the environment of the node group where the error was encountered.
-   `group`: The name of the node group where the error was encountered. Note that, due to inheritance, this may not be the group where the parameter was defined.
-   `defined_by`: The name of the node group that defines the class or parameter.

|

`missing-parent`

If the parent of the node group does not exist, the service returns a `422: Unprocessable Entity` response. The response object uses the following keys:

|Key|Definition|
|---|----------|
|`kind`|"missing-parent"|
|`msg`|Shows the parent UUID that did not exist.|
|`details`|The full submitted node group.|

`inheritance-cycle`

If the request causes an inheritance cycle, the service returns a `422: Unprocessable Entity` response. The response object uses the following keys:

|Key|Definition|
|---|----------|
|`kind`|"inheritance-cycle"|
|`details`|An array of node group objects that includes each node group involved in the cycle|
|`msg`|A shortened description of the cycle, including a list of the node group names with each followed by its parent until the first node group is repeated.|

