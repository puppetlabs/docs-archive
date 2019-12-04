# Tuning monolithic installations

Tuning information for monolithic installations varies based on size of the installation.

## Tuning monolithic installations

Use these guidelines to tune your monolithic installation, depending on the capacity of your infrastructure.

The hardware recommendations provide an overview of what you'll need to consider, but note these recommendations may vary depending on the size and complexity of your infrastructure.

### Tuning for 4 cores, 8 GB of RAM

|Install type|Puppet Server JRuby instances|Puppet Server heap \(MB\)|PuppetDB command processing threads|PuppetDB heap \(MB\)|PuppetDB broker memory|Console services heap \(MB\)|ActiveMQ heap \(MB\)|Orchestration services heap \(MB\)|PostgreSQL shared\_buffers \(MB\)|PostgreSQL work\_mem \(MB\)|Total fixed memory usage \(MB\)|
|------------|-----------------------------|-------------------------|-----------------------------------|--------------------|----------------------|----------------------------|--------------------|----------------------------------|---------------------------------|---------------------------|-------------------------------|
|Default|3|2048|2|256|179.2|256|512|192|2048|4|5312|
|Monolithic|2|2048|1|256|179.2|256|512|192|2048|4|5312|
|Monolithic plus compile masters|2|1024|2|1024|716.8|256|512|192|2048|4|5056|

### Tuning for 8 cores, 16 GB of RAM

|Install type|Puppet Server JRuby instances|Puppet Server heap \(MB\)|PuppetDB command processing threads|PuppetDB heap \(MB\)|PuppetDB broker memory|Console services heap \(MB\)|ActiveMQ heap \(MB\)|Orchestration services heap \(MB\)|PostgreSQL shared\_buffers \(MB\)|PostgreSQL work\_mem \(MB\)|Total fixed memory usage \(MB\)|
|------------|-----------------------------|-------------------------|-----------------------------------|--------------------|----------------------|----------------------------|--------------------|----------------------------------|---------------------------------|---------------------------|-------------------------------|
|Default|4|2048|4|256|179.2|256|512|192|4096|4|7360|
|Monolithic|6|3072|2|1024|716.8|512|1024|384|4096|4|10112|
|Monolithic plus compile masters|4|2048|5|3072|1024|512|1024|384|4096|4|10624|

### Tuning 16 cores, 32 GB of RAM

|Install type|Puppet Server JRuby instances|Puppet Server heap \(MB\)|PuppetDB command processing threads|PuppetDB heap \(MB\)|PuppetDB broker memory|Console services heap \(MB\)|ActiveMQ heap \(MB\)|Orchestration services heap \(MB\)|PostgreSQL shared\_buffers \(MB\)|PostgreSQL work\_mem \(MB\)|Total fixed memory usage \(MB\)|
|------------|-----------------------------|-------------------------|-----------------------------------|--------------------|----------------------|----------------------------|--------------------|----------------------------------|---------------------------------|---------------------------|-------------------------------|
|Default|4|2048|2|256|179.2|256|512|192|4096|4|7360|
|Monolithic|10|5120|4|2048|1024|1024|2048|768|4096|4|15104|
|Monolithic plus compile masters|4|2048|10|6144|1024|1024|2048|768|4096|4|16128|

**Related topics**  


[Hardware requirements](hardware_requirements.md#)

**Related topics**  


[Tune the maximum number of JRuby instances](config_puppetserver.md#)

[Configure command processing threads](config_puppetdb.md#)

[Configuring broker memory](config_puppetdb.md#)

[Configuring Java arguments for Puppet Enterprise](config_java_args.md#)

