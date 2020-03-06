# PE and open source version numbers

In October 2018, the "x.y.z" version numbering system for Puppet Enterprise \(PE\) changed so that the first number \("x"\) changes only when PE adopts a new major version of the Puppet Platform.

We made this change to so that it's easier for you to determine whether a release contains potentially high-impact breaking changes. Previously, the "x" part of the version number incremented with each new calendar year, whether or not there were breaking changes in the release.

The "y" and "z" numbers continue to increment with minor and patch releases, respectively.

|Type of change|Major "x" releases|Minor "y" releases|Patch "z" releases|
|--------------|------------------|------------------|------------------|
|Security updates|yes|yes|yes|
|Bug fixes|yes|yes|yes|
|Minor improvements|yes|yes|yes|
|Major improvements|yes|yes|no|
|New features|yes|yes|no|
|Low-impact breaking changes|yes|yes|no|
|High-impact breaking changes|yes|no|no|

For example, if you are using PE 2018.1.3, then upgrading to PE 2019 likely introduces changes that involve updating your Puppet code, depending on what features you use and what we added. Upgrading to 2018.1.4 fixes bugs and security problems, and might slightly adjust how a feature looks. 

**Note:** This version of documentation represents the latest update in this release stream. There might be differences in features or functionality from previous releases in this stream.

The first release to use the updated system is Puppet Enterprise 2019.0. Older release streams follow the previous version numbering system.

For information about active PE releases, mainstream and extended support, and end of life dates, see [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).

## Open source version numbers

All of our open source projects—including Puppet, PuppetDB, Facter, and Hiera—use semantic versioning \("semver"\). This means that in an x.y.z version number, the "y" increases if new features are introduced and the "x" increases if existing features change or get removed.

Our semver refers to only the code within that project; it's possible that packaging or interactions with other projects might cause new behavior in a "z" upgrade of Puppet.

**Note:** In Puppet versions prior to 3.0.0 and Facter versions prior to 1.7.0, we didn't use semver.

