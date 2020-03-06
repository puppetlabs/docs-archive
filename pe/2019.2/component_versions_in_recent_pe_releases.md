# Component versions in recent PE releases

These tables show which components are in Puppet Enterprise \(PE\) releases, covering recent long-term supported \(LTS\) releases. To see component version tables for a release that has passed its support phase, switch to the docs site for that release.

## Puppet Enterprise agent and server components

This table shows the components installed on all agent nodes.

**Note:** Hiera 5 is a backwards-compatible evolution of Hiera, which is built into Puppet 4.9.0 and higher. To provide some backwards-compatible features, it uses the classic Hiera 3.x.x codebase version listed in this table.

|PE Version|Puppet Agent|Puppet|Facter|Hiera|Resource API|MCollective|Ruby|OpenSSL|
|----------|------------|------|------|-----|------------|-----------|----|-------|
|2019.2.2|6.10.1|6.10.1|3.14.5|3.6.0|1.8.9|N/A|2.5.7|1.1.1d|
|2019.2.1|6.10.1|6.10.1|3.14.5|3.6.0|1.8.9|N/A|2.5.7|1.1.1d|
|2019.2.0|6.10.1|6.10.1|3.14.5|3.6.0|1.8.9|N/A|2.5.7|1.1.1d|
|2019.1.3|6.4.4|6.4.4|3.13.4|3.5.0|1.8.9|N/A|2.5.7|1.1.1d|
|2019.1.1|6.4.3|6.4.3|3.13.3|3.5.0|1.8.6|N/A|2.5.3|1.1.1a|
|2019.1.0|6.4.2|6.4.2|3.13.2|3.5.0|1.8.2|N/A|2.5.3|1.1.1a|
|2019.0.4|6.1.10|6.0.10|3.12.5|3.4.6|1.6.5|N/A|2.5.3|1.1.1a|
|2019.0.3|6.0.9|6.0.9|3.12.4|3.4.6|1.6.4|N/A|2.5.3|1.1.1a|
|2019.0.2|6.0.5|6.0.5|3.12.3|3.4.6|1.6.3|N/A|2.5.1|1.0.2n|
|2019.0.1|6.0.4|6.0.4|3.12.1|3.4.5|1.6.2|N/A|2.5.1|1.0.2n|
|2019.0.0|6.0.2|6.0.2|3.12.0|3.4.5|1.6.0|N/A|2.5.1|1.1.0h|
|2018.1.11 \(LTS\)|5.5.17|5.5.17|3.11.10|3.4.6|N/A|2.12.5|2.4.9|1.0.2t|
|2018.1.9|5.5.16|5.5.16|3.11.9|3.4.6|N/A|2.12.4|2.4.5|1.0.2r|
|2018.1.8|5.5.14|5.5.14|3.11.8|3.4.6|N/A|2.12.4|2.4.5|1.0.2r|
|2018.1.7|5.5.10|5.5.10|3.11.7|3.4.6|N/A|2.12.4|2.4.5|1.0.2n|
|2018.1.5|5.5.8|5.5.8|3.11.6|3.4.5|N/A|2.12.4|2.4.4|1.0.2n|
|2018.1.4|5.5.6|5.5.6|3.11.4|3.4.4|N/A|2.12.3|2.4.4|1.0.2n|
|2018.1.3|5.5.4|5.5.3|3.11.3|3.4.3|N/A|2.12.2|2.4.4|1.0.2n|
|2018.1.2|5.5.3|5.5.2|3.11.2|3.4.3|N/A|2.12.2|2.4.4|1.0.2n|
|2018.1.0|5.5.1|5.5.1|3.11.1|3.4.3|N/A|2.12.1|2.4.4|1.0.2n|

This table shows components installed on server nodes.

**Note:** FIPS-compliant PE version 2019.2 includes PostgreSQL version 9.6.

|PE Version|Puppet Server|PuppetDB|r10k|Razor Server|Bolt Services|Agentless Catalog Executor \(ACE\) Services|PostgreSQL|Java|ActiveMQ|Nginx|
|----------|-------------|--------|----|------------|-------------|-------------------------------------------|----------|----|--------|-----|
|2019.2.2|6.7.1|6.7.3|3.3.3|1.9.6|1.33.0|1.0.0|11.5|1.8.0|N/A|1.16.1|
|2019.2.1|6.7.1|6.7.3|3.3.3|1.9.6|1.33.0|1.0.0|11.5|1.8.0|N/A|1.16.1|
|2019.2.0|6.7.1|6.7.2|3.3.3|1.9.6|1.33.0|1.0.0|11.5|1.8.0|N/A|1.16.1|
|2019.1.3|6.3.2|6.3.6|3.2.3|1.9.5|1.34.0|1.0.0|9.6.15|1.8.0|N/A|1.16.1|
|2019.1.1|6.3.1|6.3.4|3.2.0|1.9.5|1.26.0|0.9.1|9.6.13|1.8.0|N/A|1.14.2|
|2019.1.0|6.3.0|6.3.2|3.2.0|1.9.5|1.17.0|0.9.1|9.6.12|1.8.0|N/A|1.14.2|
|2019.0.4|6.0.5|6.0.4|3.0.4|1.9.4|1.26.0|N/A|9.6.13|1.8.0|N/A|1.14.2|
|2019.0.3|6.0.4|6.0.3|3.0.3|1.9.4|1.15.0|N/A|9.6.12|1.8.0|N/A|1.14.2|
|2019.0.2|6.0.3|6.0.2|3.0.3|1.9.3|1.10.0|N/A|9.6.10|1.8.0|N/A|1.14.0|
|2019.0.1|6.0.2|6.0.1|3.0.3|1.9.3|1.1.0|N/A|9.6.10|1.8.0|N/A|1.14.0|
|2019.0.0|6.0.1|6.0.0|3.0.2|1.9.3|0.24.0|N/A|9.6.10|1.8.0|N/A|1.14.0|
|2018.1.11 \(LTS\)|5.3.10|5.2.11|2.6.7|1.9.2|N/A|N/A|9.6.15|1.8.0|5.15.5|1.16.1|
|2018.1.9|5.3.9|5.2.9|2.6.6|1.9.2|N/A|N/A|9.6.13|1.8.0|5.15.5|1.14.2|
|2018.1.8|5.3.8|5.2.8|2.6.5|1.9.2|N/A|N/A|9.6.12|1.8.0|5.15.5|1.14.2|
|2018.1.7|5.3.7|5.2.7|2.6.5|1.9.2|N/A|N/A|9.6.10|1.8.0|5.15.5|1.14.0|
|2018.1.5|5.3.6|5.2.6|2.6.5|1.9.2|N/A|N/A|9.6.10|1.8.0|5.15.5|1.14.0|
|2018.1.4|5.3.5|5.2.4|2.6.2|1.9.2|N/A|N/A|9.6.10|1.8.0|5.15.3|1.14.0|
|2018.1.3|5.3.4|5.2.4|2.6.2|1.9.2|N/A|N/A|9.6.8|1.8.0|5.15.3|1.14.0|
|2018.1.2|5.3.3|5.2.2|2.6.2|1.9.2|N/A|N/A|9.6.8|1.8.0|5.15.3|1.12.1|
|2018.1.0|5.3.2|5.2.2|2.6.2|1.8.1|N/A|N/A|9.6.8|1.8.0|5.15.3|1.12.1|

## Master and agent compatibility

Use this table to verify that you're using a compatible version of the  agent for your PE or Puppet master.

| | |Master|
| | |PE 3.x

 Puppet 3.x|PE 2015.1 through 2017.2

 Puppet 4.x|PE 2017.3 through 2018.1

 Puppet 5.x|PE 2019.1 and later

 Puppet 6.x|
|--|--|------|
|--|--|-------------------|-------------------------------------|-------------------------------------|--------------------------------|
|Agent|3.x|✓|✓|✓|✓|
|4.x| |✓|✓|✓|
|5.x| | |✓|✓|
|6.x| | | |✓|

**Note:**

-   Puppet 3.x has reached end of life and is not actively developed or tested. We retain agent 3.x compatibility with later versions of the master only to enable upgrades.
-   You can use pre-6.x agents with a Puppet 6.x or PE 2019.0 or later master, but this combination doesn't take advantage of the new intermediate certificate authority architecture introduced in Puppet Server 6.0. To adopt the new CA architecture, both your master and agents must be upgraded to at least 6.x/2019.0, and you must regenerate certificates. If you don't upgrade *all *of your nodes to 6.x, do not regenerate your certificates, because pre-6.x agents won't work with the new CA architecture. 


## Task compatibility

This table shows which version of the Puppet task specification is compatible with each version of PE.

|PE version|[Puppet task specification](https://github.com/puppetlabs/puppet-specifications/blob/master/tasks/README.md) \( GitHub\)|
|----------|------------------------------------------------------------------------------------------------------------------------|
|2019.0.1+|version 1, revision 3|
|2019.0.0+|version 1, revision 2|
|2017.3.0+|version 1, revision 1|

