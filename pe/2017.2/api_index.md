---
layout: default
title: "Index of Puppet APIs"
canonical: "/pe/latest/api_index.html"
---

Puppet provides a number of APIs that allow you to interact with Puppet applications. On this page, you'll find a brief description of each API's core tasks to help you figure out when to use which API.

<table>
  <tr>
    <th>API</th>
    <th>Useful for</th>
  </tr>
  <tr>
    <td><a href="{{puppetdb}}/api/index.html">PuppetDB APIs</a></td>
    <td><ul>
      <li>Querying the data that PuppetDB collects from Puppet</li>
      <li>Importing/exporting PuppetDB archives</li>
      <li>Changing PuppetDB's model of a population</li>
      <li>Querying information about the PuppetDB server</li>
      <li>Querying PuppetDB metrics</li>
     </ul></td>
  </tr>
  <tr>
    <td><a href="./rbac_serviceindex_v1.html">RBAC service API v1</a></td>
    <td><ul>
    <li>Managing access to Puppet Enterprise</li>
    <li>Connecting to external directories</li> 
    <li>Generating authentication tokens</li>
    <li>Managing users, user roles, user groups, and user permissions.</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="./rbac_serviceindex_v2.html">RBAC service API v2</a></td>
    <td><ul>
    <li>Revoking authentication tokens</li>
    </ul></td>
  </tr>
   <tr>
    <td><a href="./nc_index.html">Node classifier service API</a></td>
    <td><ul>
    <li>Querying the groups that a node matches</li>
    <li>Querying the classes, parameters, and variables that have been assigned to a node or group</li>
    <li>Querying the environment that a node is in</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="./orchestrator_api_endpoints.html">Puppet orchestrator API</a></td>
    <td><ul>
    <li>Gathering details about the orchestrator jobs you run</li>
    <li>Inspecting applications and applications instances in your Puppet environments</li>
    </ul></td>
  </tr>
  <tr>
    <td><p>Code Manager API</p>
      <ul>
        <li><a href="./code_mgr_webhook.html#webhook-endpoint"><code>webhook</code>endpoint</a></li>
        <li><a href="./code_mgr_scripts.html#deploys-endpoint"><code>deploys</code>endpoint</a></li>
        <li><a href="./code_mgr_scripts.html#status-endpoint"><code>status</code>endpoint</a></li>
      </ul></td>
    <td><ul>
    <li>Creating a custom URL that is used to trigger Code Manager</li>
    <li>Queueing Puppet code deploys</li>
    <li>Checking Code Manager and file sync status</li>
    </ul></td>
  </tr>
  <tr>
    <td><p>Puppet Server administrative API</p>
      <ul>
        <li><a href="{{puppetserver}}/admin-api/v1/environment-cache.html"><code>environment-cache</code>endpoint</a></li>
        <li><a href="{{puppetserver}}/admin-api/v1/jruby-pool.html"><code>jruby-pool</code>endpoint</a></li>
      </ul></td>
    <td><ul>
    <li>Deleting environment caches created by a Puppet master</li>
    <li>Deleting Puppet Server's pool of JRuby instances</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="./status_api.html">Status API</a></td>
    <td><ul>
    <li>Checking the health status of PE services</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="./rbac_activityapis.html">Activity service API</a></td>
    <td><ul>
    <li>Querying PE service and user events logged by the activity service</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="./razor_reference.markdown">Razor API</a></td>
    <td><ul>
    <li>Provisioning bare-metal machines</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="https://forgeapi.puppetlabs.com">Forge API</a></td>
    <td><ul>
    <li>Finding information about modules and users on the Puppet Forge</li>
    <li>Writing scripts and tools that interact with the Forge website</li>
    </ul></td>
  </tr>
  <tr>
    <td><a href="{{puppet}}/http_api/http_certificate.html">Certificate Authority (CA) API</a></td>
    <td><p>Used internally by Puppet to manage Puppet agent certificates. See also:</p>
    <ul><li><a href="{{puppet}}/subsystem_agent_master_comm.html">Agent/Master HTTPs Communications page</a> for information about how this API is used internally by Puppet</li>
    <li><a href="{{puppet}}/services_commands.html#puppet-cert">Puppet's Commands page</a> for information about the Puppet Cert command line tool</li></ul>
    </td>
  </tr>
  <tr>
    <td><a href="{{puppet}}/http_api/http_api_index.html">Puppet API</a></td>
    <td><ul>
    <li>Retrieving a catalog for a node</li> 
    <li>Accessing environment information</li></ul>
    <p>For more information on how this API is used internally by Puppet, see <a href="{{puppet}}/subsystem_agent_master_comm.html">Agent/Master HTTPs Communications page</a></p>
    </td>
  </tr>
  <tr>
     <td><a href="./code_mgr.html">File sync API</a></td>
     <td>Puppet only supports file sync through use of <a href="./code_mgr.html">Code Manager</a>. Directly accessing the file sync API is not supported and not recommended.</td>
  </tr>
</table>
