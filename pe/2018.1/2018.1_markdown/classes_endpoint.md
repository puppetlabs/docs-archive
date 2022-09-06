---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Classes endpoint

Use the `classes` endpoints to retrieve lists of classes, including classes with specific environments. The output from this endpoint is especially useful for creating new node groups, which usually contain a reference to one or more classes.

The node classifier gets its information about classes from Puppet, so don't use this endpoint to create, update, or delete classes.

## GET /v1/classes

Use the `/v1/classes` endpoint to retrieve a list of all classes known to the node classifier.

**Note:** All other operations on classes require using the environment-specific endpoints.

All `/classes` endpoints return the classes *currently known* to the node classifier, which retrieves them periodically from the master. To force an update, use the `update_classes` endpoint. To determine when classes were last retrieved from the master, use the `last_class_update` endpoint.

### Response format

The response is a JSON array of class objects. Each class object contains these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the class \(a string\).

|
|`environment`|The name of the environment that this class exists in. Note that the same class can exist in different environments and can have different parameters in each environment.

|
|`parameters`|An object describing the parameters and default parameter values for the class. The keys of this object are the parameter names \(strings\). Each value is the default value for the associated parameter as a string, or `null`. If the value is `null`, the parameter is required.

|

This is an example of a class object:

```json
{
  "name": "apache",
  "environment": "production",
  "parameters": {
    "default_mods": true,
    "default_vhost": true,
    ...
  }
}
```

## GET /v1/environments/<environment\>/classes

Use the `/v1/environments/\<environment\>/classes` to retrieve a list of all classes known to the node classifier within the given environment.

### Response format

The response is a JSON array of class objects. Each class object contains these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the class \(a string\).

|
|`environment`|The name of the environment that this class exists in. Note that the same class can exist in different environments and can have different parameters in each environment.

|
|`parameters`|An object describing the parameters and default parameter values for the class. The keys of this object are the parameter names \(strings\). Each value is the default value for the associated parameter as a string, or `null`. If the value is `null`, the parameter is required.

|

### Error responses

This request does not produce error responses.

## GET /v1/environments/<environment\>/classes/<name\>

Use the `/v1/environments/\<environment\>/classes/\<name\>` endpoint to retrieve the class with the given name in the given environment.

### Response format

If the class exists, the response is a JSON array of class objects. Each class object contains these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the class \(a string\).

|
|`environment`|The name of the environment that this class exists in. Note that the same class can exist in different environments and can have different parameters in each environment.

|
|`parameters`|An object describing the parameters and default parameter values for the class. The keys of this object are the parameter names \(strings\). Each value is the default value for the associated parameter as a string, or `null`. If the value is `null`, the parameter is required.

|

### Error responses

If the class with the given name cannot be found, the service returns a 404 Not Found response with an empty body.

