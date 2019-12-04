# Writing configuration files

Puppet supports two formats for configuration files that configure settings: valid JSON and Human-Optimized Config Object Notation \(HOCON\), a JSON superset.

For more information about HOCON itself, see the [HOCON documentation](https://github.com/typesafehub/config#using-hocon-the-json-superset).

## Configuration file syntax

Refer to these examples when you're writing configuration files to identify correct JSON or HOCON syntax.

### Brackets

In HOCON, you can omit the brackets \(`{ }`\) around a root object.

|JSON example|HOCON example|
|------------|-------------|
|```
{
   "authorization": {
    	"version": 1
    }
}
```

|```
"authorization": {
    "version": 1
}
```

|

### Quotes

In HOCON, double quotes around key and value strings are optional in most cases. However, double quotes are required if the string contains the characters `*`, `^`, `+`, `:`, or `=`.

|JSON example|HOCON example|
|------------|-------------|
|```
"authorization": {
    "version": 1
}
```

|```
authorization: {
    version: 1
}
```

|

### Commas

When writing a map or array in HOCON, you can use a new line instead of a comma.

| |JSON example|HOCON example|
|--|------------|-------------|
|Map|```
rbac: {
 password-reset-expiration: 24,
 session-timeout: 60,
 failed-attempts-lockout: 10,
}
```

|```
rbac: {
 password-reset-expiration: 24
 session-timeout: 60
 failed-attempts-lockout: 10
}
```

|
|Array|```
http-client: {
    ssl-protocols: [TLSv1, TLSv1.1, TLSv1.2]
}
```

|```
http-client: {
    ssl-protocols: [
    TLSv1
    TLSv1.1
    TLSv1.2
    ]
}
```

|

### Comments

Add comments using either `//` or `#`. Inline comments are supported.

|HOCON example|
|-------------|
|```
authorization: {
    version: 1
    rules: [
        {
            # Allow nodes to retrieve their own catalog
            match-request: {
                path: "^/puppet/v3/catalog/([^/]+)$"
                type: regex
                method: [get, post]
            }
        }
    ]
}
```

|

