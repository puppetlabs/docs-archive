---
layout: default
title: "Orchestration services known issues"
canonical: "/pe/latest/release_notes_known_issues_app.html"
---

This page lists known issues for orchestration services in Puppet Enterprise.


## Agents in Puppet job runs may request incorrect environment

When you use the Puppet orchestrator `puppet job` command with the `environment` flag, Puppet agent runs participating in the job should request the specified environment. A known issue exists where these agent runs may request a catalog for a different environment than the one specified in the job.

To workaround this issue, we recommend that you [use the node classifier in the PE console](./console_classes_groups.html#adding-nodes-to-a-node-group) to add agents to the environment that will be participating in Orchestration jobs. Alternatively, you could [assign the agent's environment in its configuration file]({{puppet}}/environments_assigning.html#assigning-environments-via-the-agents-config-file).

## Workarounds for PXP command whitelist validation and PCP validation vulnerabilities

The following workarounds are available to address the [Puppet Execution Protocol (PXP) Command Whitelist Validation Vulnerability](https://puppet.com/security/cve/pxp-agent-oct-2016) and the [Puppet Communications Protocol (PCP) Broker String Validation Vulnerability](https://puppet.com/security/cve/pcp-broker-oct-2016). 

As a first priority we recommend upgrading to PE 2016.4.0, which contains a fix for this issue. If you can't immediately upgrade, we recommend using one of the following workarounds.

### Disable the PXP agent on all nodes

In this workaround, you disable the PXP agent on all agent nodes. With this workaround, you can't run Puppet orchestrator jobs or trigger Puppet runs in the console.

This is a workaround for [Puppet Execution Protocol (PXP) Command Whitelist Validation Vulnerability](puppet.com/security/cve/pxp-agent-oct-2016).

1. Add this class to your module directory or control repo.


   ```puppet
   class pcp_fix::disable_agent {
     service { 'pxp-agent':
       ensure     => stopped,
       enable     => false,
       hasrestart => true,
     }
   }
   ```

2. In the PE console, click **Nodes** > **Classification** and in the **PE Infrastructure** group, select the **PE Agent** group.
3. On the **Classes** tab, add the class `pcp_fix::disable_agent`.
4. In the `puppet_enterprise::profile::agent` class, set the **pxp_enabled** parameter to `false`. 

   >**Note:** If you have any nodes that are not in the **PE Agent group**, repeat steps 3 and 4 for those nodes. 

5. On all agent nodes, run Puppet. 

### Use tk-auth to set authorization rules

If you need to use Puppet orchestrator or trigger Puppet runs in the console, you can set tk-auth rules to prevent agents from triggering Puppet runs.

This is a workaround for [Puppet Communications Protocol (PCP) Broker String Validation Vulnerability](puppet.com/security/cve/pcp-broker-oct-2016).

1. Add this class to your module directory or control repo.
   
   ```puppet
   class pcp_fix::broker_auth {
     $orchestration_auth = "/etc/puppetlabs/orchestration-services/conf.d/authorization.conf"

     pe_puppet_authorization::rule { 'pcp-broker message - explicit allow':
       match_request_path    => '/pcp-broker/send',
       match_request_type    => 'path',
       match_request_query_params => {
         'message_type' => [
           'http://puppetlabs.com/associate_request',
           'http://puppetlabs.com/inventory_request',
           'http://puppetlabs.com/inventory_response',
           'http://puppetlabs.com/error_message',
           'http://puppetlabs.com/rpc_provisional_response',
           'http://puppetlabs.com/rpc_blocking_response',
           'http://puppetlabs.com/rpc_non_blocking_response',
           'http://puppetlabs.com/rpc_error_message',
         ],
       },
       allow_unauthenticated => true,
       sort_order            => 410,
       path                  => $orchestration_auth,
       notify     => Service['pe-orchestration-services'],
     }

     pe_puppet_authorization::rule { 'pcp-broker message - default deny':
       match_request_path    => '/pcp-broker/send',
       match_request_type    => 'path',
       deny                  => '*',
       sort_order            => 419,
       path                  => $orchestration_auth,
       notify     => Service['pe-orchestration-services'],
     }
   }
   ```

2.  In the PE console, click **Nodes** > **Classification** and in the **PE Infrastructure** group, select the **PE Orchestrator** group.
3. On the **Classes** tab, add the class `pcp_fix::broker_auth`.
4. On the Puppet master, run Puppet.  







