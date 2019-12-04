---
layout: default
title: "UTF-8 known issues"
canonical: "/pe/latest/release_notes_known_issues_utf8.html"
---

This page lists known issues with UTF-8 support in Puppet and Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Installation fails on systems that use UTF-8 characters in file names

If you attempt to install Puppet Enterprise on a system that uses UTF-8 characters in path or file names, installation fails with a message indicating errors in the pe.conf file. <!--PE-18165-->

## On Windows, `puppet code deploy` fails if you specify a token with UTF-8 characters

On Windows systems, `puppet code deploy` fails if you specify an authentication token that includes UTF-8 characters in the filename. This failure occurs only if you specify the token in the `puppet-code.conf` file or on the command line with the `--token-file` option. The error message includes an exception like:

```
Unhandled exception: No authentication token exists at path C:/Users/Administrator/PathWithUTF-8. Please log in with `puppet access login`
```

<!--CODEMGMT-1026-->

## PE console password must be ASCII

Supported web browsers do not accept non-ASCII characters in a login password field. In the pe.conf file, you can specify a non-ASCII password for the console admin password, but you will not be able to use the password to log in to the PE console in your browser.

## Non-ASCII characters not supported in some names in the Puppet language

When naming the following items in Puppet, you must use ASCII characters:

- Environment names
- Variable names
- Class names
- Resource type names
- Module names
- Parameter names
- Tag names

See [the Puppet language documentation](https://docs.puppet.com/puppet/latest/lang_reserved.html#acceptable-characters-in-names) for more information.

## Some operating systems do not allow non-ASCII names for several types of resources

Some operating systems only allow ASCII characters in the `title` and `namevar` of certain resource types. For example, the user and group resources on RHEL and CentOS may only contain ASCII characters in `title` and `namevar`. For information about the user resource type, see [Resource Type Reference](https://docs.puppet.com/puppet/latest/type.html#user).

## Non-ASCII file names can cause errors when used in concat fragments

When using concat fragments, an error occurs if a `source` attribute is specified, and the `source` attribute points to a file name containing non-ASCII characters.

## Non-ASCII file names can cause errors in the file resource

In a file resource, an error occurs if the `source` attribute is set to a file name containing non-ASCII characters.

## Ruby can corrupt the `path` fact and environment variable on Windows

There is a bug in Ruby that can corrupt the environment variable names and values. This bug only causes corruption for some codepages.

The same bug can cause the `path` fact to be cached in a corrupt state.




