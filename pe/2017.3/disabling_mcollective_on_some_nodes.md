# Disabling MCollective on some nodes

By default, PE enables and configures MCollective on all agent nodes. This is generally desirable, but the code that manages this will not work on non-PE agent nodes, and will cause run failures on them.

Since the Puppet master server supports managing non-PE agent nodes \(including things like network devices\), you should disable MCollective on these nodes.

To disable MCollective for a node, in the console, create a rule in the `PE MCollective` group that excludes the node. This will prevent PE from attempting to enable MCollective on that node. [See here](grouping_and_classifying_nodes.md#)for instructions on including nodes in node groups in the console.

