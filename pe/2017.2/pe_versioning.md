---
layout: default
title: "Puppet Enterprise version numbers"
canonical: "/pe/latest/pe_versioning.html"

---

{:.concept}
In July 2015, Puppet Enterprise moved to a new versioning system. This system follows an "x.y.z" pattern, where "x" is the year of the release, "y" is the ordered number of the release within the year, and "z" reflects a patch/bugfix release.

We made this change to align with our release cadence for Puppet Enterprise, which is based on time, rather than number of features. Additionally, time-based versioning makes it easy for you to determine how current your version of Puppet Enterprise is.

The first release to use this system was Puppet Enterprise 2015.2. Versions of PE released before PE 2015.2 (PE 3.8 and earlier) will keep their original version numbers.

{:.concept}
## Puppet's open source projects

All of our open source projects — including Puppet, PuppetDB, Facter, and Hiera — use semantic versioning ("semver"). This means that in an x.y.z version number, the "y" will increase if new features are introduced and the "x" will increase if existing features change or get removed.

Our semver only refers to the code within that project; it's possible that packaging or interactions with other projects might cause new behavior in a "z" upgrade of Puppet.

**Historical note:** In Puppet versions prior to 3.0.0 and Facter versions prior to 1.7.0, we didn't use semvar.