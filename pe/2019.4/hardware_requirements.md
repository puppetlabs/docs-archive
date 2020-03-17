# Hardware requirements

These hardware requirements are based on internal testing at Puppet and are meant only as guidelines to help you determine your hardware needs.

Your installation's existing code base can significantly affect performance, so adjusting expectations based on differences between your installation’s code base and the testing code base as documented in [How we develop hardware requirements](hardware_requirements.md#) is important to make the best use of the requirements on this page.

**Parent topic:**[System requirements](system_requirements.md)

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning standard installations](tuning_monolithic.md#)

## Hardware requirements for standard installations

In standard installations, hardware requirements differ depending on the number of nodes you're managing. To take advantage of progressively larger hardware, you must configure your environment to use additional resources.

|Node volume|Cores|RAM|/opt/|/var/|AWS EC2|Azure|
|-----------|-----|---|-----|-----|-------|-----|
|Trial use|2|8 GB|20 GB|24 GB|m5.large|A2 v2|
|Up to 2,000|4|8 GB|50 GB|24 GB|c5.xlarge|F4s v2|
|Up to 3,500|8|16 GB|80 GB|24 GB|c5.2xlarge|F8s v2|
|Up to 4,000|16|32 GB|100 GB|24 GB|c5.4xlarge|F16s v2|

Important notes on the data in this chart:

-   **Trial mode:** Although the m5.large instance type is sufficient for trial use, it is not supported. A minimum of four cores is required for production workloads.
-   **Azure:** Azure requirements are not currently tested by Puppet, but are presented here as our best guidance based on comparable EC2 instance testing.
-   **/opt/ storage requirements:** The database should not exceed 50% of /opt/ to allow for future upgrades.
-   **/var/ storage requirements:** There are roughly 20 log files stored in /var/ which are limited in size to 1 GB each. Log retention settings make it unlikely that the maximum capacity will be needed, but we recommend allocating 24 GB to avoid issues.

## Hardware requirements for large installations

If you are managing more than 4,000 nodes, you can add load-balanced compilers to your installation to increase the number of agents you can manage.

Each compiler increases capacity by approximately 1,500–3,000 nodes, until you exhaust the capacity of PuppetDB or the console, which run on the master. If you start to see performance issues around 8,000 nodes, you can adjust your hardware or move to a larger base infrastructure.

**Note:** When you expand your deployment to use compilers, you must also start using load balancers. It is simpler to upgrade your hardware in your installation, if you can, than to add compilers and load balancers.

To manage more than 4,000 nodes, we recommend the following minimum hardware:

|Node volume|Node|Cores|RAM|/opt/|/var/|EC2|
|-----------|----|-----|---|-----|-----|---|
|4,000–20,000|Primary node|16|32|150|10|c5.4xlarge|
|Each compiler \(1,500 - 3,000 nodes\)|4|8|30|2|m5.xlarge|

## Hardware requirements for extra-large installations

You can continue to expand your installation beyond 20,000 nodes by adding compilers and increasing the size of your master and PE-PostgreSQL nodes.

You can also increase the number of nodes you support by tuning certain settings such as the run interval \(changing how often agents check in\) and `report-ttl` \(changing how long PuppetDB stores reports\).

To manage more than 4,000 nodes, we recommend the following minimum hardware:

|Node volume|Node|Cores|RAM|/opt/|/var/|EC2|
|-----------|----|-----|---|-----|-----|---|
|20,000+|Primary node|16|32|150|10|c5.4xlarge|
|Each compiler \(1,500 - 3,000 nodes\)|4|8|30|2|m5.xlarge|
|PE-PostgreSQL node|16|128|300|4|r5.4xlarge|

If you manage more than 20,000 nodes, contact Puppet professional services to talk about optimizing your setup for your specific requirements

## How we develop hardware requirements: Performance test methods

Puppet tests the performance of Puppet Enterprise and develops hardware requirements based on our testing. We continue to invest in improvements in our performance testing methods.

The performance of Puppet Enterprise is highly dependent on the code base used by a specific installation. The details included here are intended to help you evaluate how your specific installation compares to what we used to generate our scale and hardware estimates so that you can better estimate what you need to manage your Puppet infrastructure.

### Tools: Gatling and `puppet infrastructure tune`

We use [gatling-puppet-load-test](https://github.com/puppetlabs/gatling-puppet-load-test), a framework we’ve developed to perform load and performance testing for PE and open source Puppet. It uses [Gatling](https://gatling.io/), an open source load and performance testing tool that records and replays HTTP traffic, then generates reports about the performance of the simulated requests. Using Gatling allows us to simulate agent requests in a full PE installation where Puppet Server is driving communication with PuppetDB, the node classifier, and other tools.

We also use `puppet infrastructure tune`, which provides configuration settings to apply to the installation.

### Platform: Amazon EC2

We use Amazon EC2 instances for our performance tests with a master node running PE and a metrics node running the simulated agents and capturing the performance data. We know that the instances' performance varies, but this has less of an impact on overall performance than the variations in the size and complexity of users' code bases.

|AWS EC2|Cores|RAM|Testing use|
|-------|-----|---|-----------|
|m5.large|2|8 GB|Confirming trial size works|
|c5.xlarge|4|8 GB|Scale|
|c5.2xlarge|8|16 GB|Scale, Apples-to-apples, Soak|
|c5.4xlarge|16|32 GB|Scale|

### Test environment

**Control repository**

We use [puppetlabs-pe\_perf\_control\_repo](https://github.com/puppetlabs/puppetlabs-pe_perf_control_repo) with r10k to classify the simulated agents using roles and profiles, with three sizes defined.

**Facts and reports**

Although the simulated agents all share the same classification, we use dynamic facts and varying report responses \(no change, failure, intentional change, corrective change\) to better simulate a typical customer environment.

| |**role::by\_size::small**|**role::by\_size::medium**|**role::by\_size::large**|
|**Resources**|129|750|1267|
|**Classes**|24|71|120|
|**Facts**|151|151|151|

**Classification**

```
class role::by_size::small {
  include ::profile::tomcat::basic
  include ::profile::postgresql::basic
}

class role::by_size::medium {
  include ::role::by_size::small

  include ::profile::users
  include ::profile::sysop::packages
  include ::profile::motd
  include ::profile::hiera_check
}

class role::by_size::large {
  include ::role::by_size::medium

  include ::profile::apache::basic
  include ::profile::influxdb::basic
  include ::profile::loop_through_file_resources
}
```

**Environment cache**

Although we recommend [enabling environment caching](https://puppet.com/docs/puppet/latest/configuration.html#environmenttimeout) to improve performance in production environments, we do not enable it in our performance testing environment to avoid unrealistic results due to the identical classification of the simulated agents.

**Fact, resource, and class counts**

You can query PuppetDB using Puppet Query Language \(PQL\) to find the number of facts, resources, and classes for a given node.

First, you will need a token with permissions to query Puppet DB. If you don't already have one, you can generate one using `puppet-access` on the master. For example:

```
puppet access login -l 1d
```

See our [Token-based authentication](rbac_token_auth_intro.md#) docs for more information on generating tokens.

Once you have a valid token, determine the number of facts, resources, and classes for the specified node using the example code below. In the examples, we used `perf-agent1` as the node name.

```
puppet query "facts[count()] {certname = 'perf-agent1'}"
```

```
puppet query "resources[count()] {certname = 'perf-agent1'}"
```

```
puppet query "resources[count()] {type = 'Class' and certname = 'perf-agent1'}"
```

For background and additional examples, check out this [blog post](https://puppet.com/blog/getting-know-puppet-query-language-pql) or view the full [PQL documentation](https://puppet.com/docs/puppetdb/latest/api/query/v4/pql.html).

### Testing methodology

**Warming up Puppet Server**

JRuby 9000 provides better overall performance than the previous version, but it also comes with a warm-up burden. For some tests we want the Puppet Server process to be warmed up before we begin. Our warm-up process involves running a portion of the agents to generate about 300 connections to the endpoints served by JRubies, multiplied by the number of configured JRuby instances. In general, the closer you are to the node capacity of your install, the faster it will warm up when running the normal load. Our process takes about five minutes.

**Apples-to-apples testing**

To do an apples-to-apples test, we run a simulation for two different PE versions of 600 agents each \(classified as large\) checking in at the default 30-minute interval for eight iterations. We run the warm-up procedure before the simulation. Then we compare response times, system resource usage, and process resource usage. We do this on a weekly basis for all active development branches to ensure that general performance doesn’t degrade during development.

**Scale testing**

In order to determine the node capacity of each targeted EC2 instance type, we perform scale testing with a small load that incrementally increases the number of simulated agents until requests begin to time out or respond with errors.

The scale test is structured to simulate the default 30-minute agent check-in interval. We start the test with an agent volume below the expected performance threshold and add 100 agents at a time until more than 10 timeouts are encountered. For example, when testing the EC2 c5.2xlarge \(8 cores and 16 GB\), we start with 3,000 agents.

**Cold-start versus warm-start scale testing**

To simulate recovering from a Puppet Server restart, we use a cold-start scenario. We restart the `pe-puppetserver` service between each iteration so that the warm-up burden will reduce the maximum node count. This is the most conservative estimate of node capacity we test for.

To determine burst capacity, we use a warm-start scenario. When doing a warm-start scale test, we don’t restart the `pe-puppetserver` service, and we run the test on the same host that ran the cold-start tests to ensure it is already warmed up.

The extra capacity provided by a warmed up system is great for serving Puppet agent runs triggered by orchestration from the console, Continuous Delivery for Puppet Enterprise, or Bolt. Therefore we recommend keeping your node count near the cold-start numbers.

**Soak testing**

To ensure PE doesn’t suffer a performance degradation over time we run a soak test on each release. We use 600 agents, classified as large, checking in at the default 30-minute interval for 14 days. We run the warm-up before we start the test. This two-week test lets us verify that all garbage collection features are being triggered and keeping things under control. We check the Gatling response time graphs to ensure that performance is stable throughout the test.



