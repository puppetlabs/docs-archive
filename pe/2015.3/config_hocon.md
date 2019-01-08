---
layout: default
title: "HOCON Configuration Format"
canonical: "/pe/latest/config_hocon.html"
---

The supported format for configuration files used to configure Puppet settings is valid JSON. In addition to JSON, we also support the following subset of [HOCON (Human-Optimized Config Object Notation)](https://github.com/typesafehub/config#using-hocon-the-json-superset): 

#### Brackets 

You can omit the `{` and `}` around a root object.

For example, instead of:

~~~
{
   "authorization": {
    	"version": 1
    }
}
~~~

you can write:

~~~ 
"authorization": {
    "version": 1
}
~~~

#### Quotes

Double quotes around key and value strings are optional. However, double quotes are required if the string contains certain characters (`*`, `^`, `+`, `:`, and `=`).

For example, instead of:

~~~ 
"authorization": {
    "version": 1
}
~~~

you can write:

~~~ 
authorization: {
    version: 1
}
~~~
 
#### Commas 

When writing a map or array, you can use a new line instead of a comma.

In a map, instead of:

~~~
    rbac: {
     password-reset-expiration: 24,
     session-timeout: 60,
     failed-attempts-lockout: 10,
    }
~~~

you can write:

~~~
    rbac: {
     password-reset-expiration: 24
     session-timeout: 60
     failed-attempts-lockout: 10
    }
~~~

In an array, instead of:

~~~
http-client: {
    ssl-protocols: [TLSv1, TLSv1.1, TLSv1.2]
}
~~~ 

you can write:

~~~
http-client: {
    ssl-protocols: [
    TLSv1
    TLSv1.1
    TLSv1.2
    ]   
}
~~~ 
 
#### Comments

You can add comments using either `//` or `#`. Inline comments are supported.

For example:

~~~
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
~~~





