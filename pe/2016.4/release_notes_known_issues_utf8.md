---
layout: default
title: "UTF-8 known issues"
canonical: "/pe/latest/release_notes_known_issues_utf8.html"
---

This page lists known issues with UTF-8 support in Puppet and Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## Node graph display errors for names containing non-ASCII characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096 see also console page-->

<!--## UTF-8 characters not supported in Puppet language names-->