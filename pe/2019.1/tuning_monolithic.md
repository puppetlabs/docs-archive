# Tuning monolithic installations

Use these guidelines to configure your installation to maximize its use of available system \(CPU and RAM\) resources.

PE is composed of multiple services on one or more infrastructure hosts. Each service has multiple settings that can be configured to maximize use of system resources and optimize performance. The default settings for each service are conservative, because the set of services sharing resources on each host varies depending on your infrastructure.

Optimized settings vary depending on the complexity and scale of your infrastructure. For example, you might need to allocate more memory per JRuby depending on the number of environments, or the number of agents and their run intervals.

Configure settings after an install or upgrade, or after making changes to infrastructure hosts, including changing the system resources of existing hosts, or adding new hosts, including compilers.



**Related information**  


[Hardware requirements](hardware_requirements.md#)

[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tune the maximum number of JRuby instances](config_puppetserver.md#)

[Configure command processing threads](config_puppetdb.md#)

[Increase the Java heap size for PE Java services](config_java_args.md#)

[Tuning the PostgreSQL buffer pool size](config_console.md#)

## Master tuning

These are the default and recommended tuning settings for your master or high availability replica.

### Tuning for 4 cores, 8 GB of RAM

|Install type|Puppet Server|PuppetDB|Console|Orchestrator|PostgreSQL|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Command processing threads|Java heap \(MB\)|Java heap \(MB\)|Java heap \(MB\)|Shared buffers \(MB\)|Work memory \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|--------|-------|------------|----------|----------|-------------|
|--------------------------|----------------|--------------------------|--------------------------|----------------|----------------|----------------|---------------------|------------------|----|----|-----------|-----------|
|Default|3|2048|512|2|256|256|192|2048|4|5|-1|5312|2880|
|Recommended|2|1024|512|1|512|512|512|2048|4|3|1|5120|3072|
|With compilers|2|1024|512|2|1024|512|512|2048|4|4|0|5632|2560|

### Tuning for 8 cores, 16 GB of RAM

|Install type|Puppet Server|PuppetDB|Console|Orchestrator|PostgreSQL|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Command processing threads|Java heap \(MB\)|Java heap \(MB\)|Java heap \(MB\)|Shared buffers \(MB\)|Work memory \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|--------|-------|------------|----------|----------|-------------|
|--------------------------|----------------|--------------------------|--------------------------|----------------|----------------|----------------|---------------------|------------------|----|----|-----------|-----------|
|Default|4|2048|1024|4|256|256|192|4096|4|8|0|7872|8512|
|Recommended|5|3840|1024|2|1024|768|768|4096|4|7|1|11520|4864|
|With compilers|2|1536|1024|4|3072|768|768|4096|4|6|2|11264|5120|

### Tuning 16 cores, 32 GB of RAM

|Install type|Puppet Server|PuppetDB|Console|Orchestrator|PostgreSQL|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Command processing threads|Java heap \(MB\)|Java heap \(MB\)|Java heap \(MB\)|Shared buffers \(MB\)|Work memory \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|--------|-------|------------|----------|----------|-------------|
|--------------------------|----------------|--------------------------|--------------------------|----------------|----------------|----------------|---------------------|------------------|----|----|-----------|-----------|
|Default|4|2048|2048|8|256|256|192|4096|4|12|4|8896|23872|
|Recommended|11|11264|2048|4|2048|1024|1024|8192|4|15|1|25600|7168|
|With compilers|4|4096|2048|8|5120|1024|1024|8192|4|12|4|21504|11264|

## Compiler tuning

These are the default and recommended tuning settings for compilers.

### Tuning 4 cores, 8 GB of RAM

|Install type|Puppet Server|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|----------|-------------|
|--------------------------|----------------|--------------------------|----|----|-----------|-----------|
|Default|3|2048|512|3|1|2560|5632|
|Recommended|3|1536|512|3|1|2048|6144|

### Tuning 8 cores, 16 GB of RAM

|Install type|Puppet Server|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|----------|-------------|
|--------------------------|----------------|--------------------------|----|----|-----------|-----------|
|Default|4|2048|1024|4|4|3072|13312|
|Recommended|7|5376|1024|7|1|6400|9984|

### Tuning 16 cores, 32 GB of RAM

|Install type|Puppet Server|CPU totals|Memory totals|
|JRuby max active instances|Java heap \(MB\)|Reserved code cache \(MB\)|Used|Free|Used \(MB\)|Free \(MB\)|
|------------|-------------|----------|-------------|
|--------------------------|----------------|--------------------------|----|----|-----------|-----------|
|Default|4|2048|2048|4|12|4096|28672|
|Recommended|15|15360|2048|15|1|17408|15360|

## Using the `puppet infrastructure tune` command

The `puppet infrastructure tune` command outputs optimized settings for PE services based on recommended guidelines.

When you run `puppet infrastructure tune` on your master, it queries PuppetDB to identify infrastructure hosts and their processor and memory facts, and outputs settings in YAML format for use in Hiera.

The `puppet infrastructure tune` command optimizes based on available system resources, not agent load or environment complexity. You can add the option `--memory_per_jruby <MB>` to optimize the Puppet Server service for environment complexity.

With the `--current` option, you can review currently specified settings for PE services. Settings might be specified in either the console or in Hiera, with console settings taking precedence over Hiera settings. Specify settings in the console or Hiera, but not both. The `--current` option identifies duplicate settings found in both places.

The `puppet infrastructure tune` command is compatible with monolithic and split infrastructures, with or without compilers, external PostgreSQL hosts, and replica hosts. You can run the command on your master, but not on compilers or high availability replicas. The command must be run as root.

For more information about the tune command, run `puppet infrastructure tune --help`.

