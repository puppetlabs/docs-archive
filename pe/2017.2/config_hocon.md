---
layout: default
title: "Writing configuration files"
canonical: "/pe/latest/config_hocon.html"
---

{:.concept}
We support two formats for configuration files that configure Puppet settings: valid JSON and HOCON (Human-Optimized Config Object Notation), a JSON superset.

Read more about [HOCON (Human-Optimized Config Object Notation)](https://github.com/typesafehub/config#using-hocon-the-json-superset)

{:.reference}
## Configuration file syntax

Refer to these examples when you're writing configuration files to identify correct JSON or HOCON syntax.

{:.section}
## Brackets

In HOCON you can omit the `{` and `}` around a root object.

<table>
  <tr>
    <th>JSON example</th>
    <th>HOCON example</th>
  </tr>
  <tr>
    <td><pre>
{
   "authorization": {
    	"version": 1
    }
}
</pre></td>
    <td><pre>"authorization": {
    "version": 1
}</pre></td>
  </tr>
</table>

{:.section}
## Quotes

In HOCON double quotes around key and value strings are optional in most cases. However, double quotes are required if the string contains the characters `*`, `^`, `+`, `:`, or `=`.

<table>
  <tr>
    <th>JSON</th>
    <th>HOCON</th>
  </tr>
  <tr>
    <td><pre>
"authorization": {
    "version": 1
}
</pre></td>
    <td><pre>
authorization: {
    version: 1
}
</pre></td>
  </tr>
</table>

{:.section}
## Commas

When writing a map or array in HOCON, you can use a new line instead of a comma.

<table>
<tr>
<th></th>
<th>JSON</th>
<th>HOCON</th>
</tr>
<tr>
<td>Map</td>
<td>
<pre>
rbac: {
 password-reset-expiration: 24,
 session-timeout: 60,
 failed-attempts-lockout: 10,
}
</pre></td>
<td>
<pre>
rbac: {
 password-reset-expiration: 24
 session-timeout: 60
 failed-attempts-lockout: 10
}
</pre></td>
</tr>
<tr>
<td>Array</td>
<td>
<pre>
http-client: {
    ssl-protocols: [TLSv1, TLSv1.1, TLSv1.2]
}
</pre></td>
<td>
<pre>
http-client: {
    ssl-protocols: [
    TLSv1
    TLSv1.1
    TLSv1.2
    ]
}
</pre></td>
</tr>
</table>

{:.section}
## Comments

Add comments using either `//` or `#`. Inline comments are supported.

<table>
  <tr>
    <th>HOCON</th>
  </tr>
  <tr>

    <td><pre>
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
</pre></td>
  </tr>
</table>