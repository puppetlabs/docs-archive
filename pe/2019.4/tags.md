---
author: melissa amos <melissa.amos@puppet.com\>
---

# Tags

A tag consists of a unique `name` and a `rule`. Tags match a node if evaluating the node against the tag's facts results in `true`. Tag matching is case sensitive.

For example, to create a tag, *small*, that matches any machine with less than 4GB of memory:

```
    razor create-tag --name small
      --rule '["<", ["num", ["fact", "memorysize_mb"]], 4128]'
```

## Tag rules

Rule expressions are of the form `op arg1 arg2 ... argn]` where `op` is one of the accepted \[operators, and `arg1` through `argn` are the arguments for the operator. If the arguments are expressions themselves, they're evaluated before `op`.

Here are some example tag rules:

-   To match nodes with more than 10 processors: `[">", ["num", ["fact", "processorcount"]], 10]`

-   To match nodes with specified MAC addresses: `["has_macaddress", "de:ea:db:ee:f0:00", "de:ea:db:ee:f0:01"]`


## Tag operators

The expression language supports these operators:

|Operator|Returns|Aliases|
|--------|-------|-------|
|`["=", arg1, arg2]`|True if the specified arguments are equal.|`"eq"`|
|`["!=", arg1, arg2]`|True if the specified arguments are not equal.|`"neq"`|
|`["and", arg1, ..., argn]`|True if all arguments are true.| |
|`["or", arg1, ..., argn]`|True if any argument is true.| |
|`["not", arg]`|True if the argument evaluates to `false` or `nil`.| |
|`["fact", arg1 (, arg2)]`|The `arg1` fact for the node. The optional `arg2` is used if the `arg1` fact isn't present.| |
|`["metadata", arg1 (, arg2)]`|The `arg` metadata entry for the node. The optional `arg2` is used if the `arg1` fact isn't present.| |
|`["tag", arg]`|True if the node has the specified tag.| |
|`["has_macaddress", arg1, arg2 ..., argn]`|True if any facts that start with "macaddress" matches one of `arg1` ... `argn`.| |
|`["has_macaddress_like", arg1, arg2 ..., argn]`|True if the hardware MAC address matches one of `arg1` ... `argn` as regular expressions.| |
|`["in", arg1, arg2, ..., argn]`|True if `arg1` matches one of `arg2` ... `argn`.| |
|`["num", arg1]`|`arg1` as a numeric value, or raises an error.| |
|`[">", arg1, arg2]`|True if `arg1` is greater than `arg2`.|`"gt"`|
|`["<", arg1, arg2]`|True if `arg1` is less than `arg2`.|`"lt"`|
|`[">=", arg1, arg2]`|True if `arg1` is greater than or equal to `arg2`.|`"gte"`|
|`["<=", arg1, arg2]`|True if `arg1` is less than or equal to `arg2`.|`"lte"`|
|`["like", arg1, arg2]`|True if `arg1` matches the pattern of `arg2`, interpreted as a regular expression.| |
|`["lower", arg]`|The lowercase version of the string `arg`.| |
|`["upper", arg]`|The uppercase version of the string `arg`.| |

**Parent topic:**[Working with Razor objects](working_with_razor_objects.md)

**Related information**  


[\(Optional\) Create tags](provisioning_a_windows_node.md#)

[Tag commands](using_the_razor_client.md#)

[Tags API](api_reference.md#)

