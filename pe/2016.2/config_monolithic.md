---
layout: default
title: "Tuning monolithic installations (reference)"
canonical: "/pe/latest/config_monolithic.html"
---

The following tables provide tuning information for various sizes of monolithic installations.

For instructions on tuning the elements listed in the tables, see the following pages: 

- [Tuning Puppet server JRuby instances](./config_puppetserver.html#tuning-jruby-on-puppet-server)
- [Command processing threads](./config_puppetdb.html#configuring-command-processing-threads)
- [Configuring broker memory](./config_puppetdb.html#configuring-broker-memory) (advanced Puppet users)
- [Tuning Java args/heap size](./config_java_args.html)
   - [Puppet server](./config_java_args.html#pe-puppet-server-service)
   - [Orchestration services](./config_java_args.html#pe-orchestration-services)
   - [PuppetDB](./config_java_args.html#puppetdb)
   - [Console services](./config_java_args.html#pe-console-services)
   - [ActiveMQ](./config_java_args.html#activemq-heap-usage-puppet-master-only)

Not sure what hardware you need? The [hardware recommendations](./install_system_requirements.html#hardware-recommendations) provides an overview of what you'll need to consider, but note these recommendations may vary depending on the size and complexity of your PE infrastructure.


## 4 cores, 8 GB of RAM

<table>
  <tr>
    <th></th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-left: 1px solid grey; border-right: 1px solid grey;">Puppet server</th>
    <th colspan="3" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PuppetDB</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;"> Node classifier</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">ActiveMQ</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">Orchestration services</th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PostgreSQL</th>
    <th style="vertical-align:middle; text-align:left">Total Fixed Memory Usage (MB)</th>
  </tr>
  <tr>
    <th style="vertical-align:middle;">Install Type</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">JRuby instances</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Command processing threads</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Broker memory</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">shared_buffers (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-left: 1px solid grey;">work_mem (MB)</th>
    <th style="border-left: 1px solid grey;"></th>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Default</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">3</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">179.2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">192</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">5312</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">179.2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">192</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">5312</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic + compile masters</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">716.8</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">192</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">5056</td>
  </tr>
</table>


## 8 cores, 16 GB of RAM 

<table>
  <tr>
    <th></th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-left: 1px solid grey; border-right: 1px solid grey;">Puppet server</th>
    <th colspan="3" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PuppetDB</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;"> Node classifier</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">ActiveMQ</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">Orchestration services</th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PostgreSQL</th>
    <th style="vertical-align:middle; text-align:left">Total Fixed Memory Usage (MB)</th>
  </tr>
  <tr>
    <th style="border-right: 1px solid grey;">Install Type</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">JRuby instances</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Command processing threads</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Broker memory</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">shared_buffers (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">work_mem (MB)</th>
    <td></td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Default</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">179.2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">192</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">7360</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">6</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">3072</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">716.8</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">384</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">10112</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic + compile masters</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">5</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">3072</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">384</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">10624</td>
  </tr>
</table>

## 16 cores, 32 GB of RAM 

<table>
  <tr>
    <th></th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-left: 1px solid grey; border-right: 1px solid grey;">Puppet server</th>
    <th colspan="3" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PuppetDB</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;"> Node classifier</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">ActiveMQ</th>
    <th style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">Orchestration services</th>
    <th colspan="2" style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">PostgreSQL</th>
    <th style="vertical-align:middle; text-align:left">Total Fixed Memory Usage (MB)</th>
  </tr>
  <tr>
    <th style="border-right: 1px solid grey;">Install Type</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">JRuby instances</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Command processing threads</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Broker memory</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">Heap (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">shared_buffers (MB)</th>
    <th style="vertical-align:middle; text-align:center; padding: 0 1rem; border-right: 1px solid grey;">work_mem (MB)</th>
    <td></td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Default</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">179.2</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">256</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">512</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">192</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">7360</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">10</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">5120</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">768</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">15104</td>
  </tr>
  <tr>
    <td style="border-right: 1px solid grey;">Monolithic + compile masters</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">10</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">6144</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">1024</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">2048</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">768</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4096</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">4</td>
    <td style="vertical-align:middle; text-align:center; border-right: 1px solid grey;">16128</td>
  </tr>
</table>