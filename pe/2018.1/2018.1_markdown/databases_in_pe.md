# Databases in Puppet Enterprise

PE uses PostgreSQL as the backend for its databases. Use the native tools in PostgreSQL to perform database exports and imports.

The PE PostgreSQL database includes the following databases:

|Database|Description|
|--------|-----------|
|pe-activity|Activity data from the Classifier, including user, nodes, and time of activity.|
|pe-classifier|Classification data, all node group information.|
|pe-puppetdb|PuppetDB data, including exported resources, catalogs, facts, and reports.|
|pe-rbac|Role-based access control data, including users, permissions, and AD/LDAP info.|
|pe-orchestrator|Orchestrator data, including user, node, and result details about job runs.|

