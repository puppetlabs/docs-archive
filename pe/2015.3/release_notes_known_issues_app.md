---
layout: default
title: "Orchestration Services Known Issues"
canonical: "/pe/latest/release_notes_known_issues_app.html"
---

## Installing and Upgrading to PE 2015.3 Requires Additional Steps to Configure the pe-orchestration Database

If you are upgrading a split install to PE 2015.3 please see [Upgrading to Puppet Enterprise 2015.3 Requires Additional Steps to Configure the pe-orchestration-database](./release_notes_known_issues_install.html#installing-and-upgrading-to-pe-20153-requires-additional-steps-to-configure-the-pe-orchestration-database) for information about additional steps you will perform during the upgrade.

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







