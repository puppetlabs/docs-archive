# Available events

These are the events for which hook scripts can be provided:

-   `node-booted`: Triggered every time a node boots via iPXE.
-   `node-registered`: Triggered after a node has been registered. Limited hardware information is available after registration.
-   `node-deleted`: Triggered after a node has been deleted.
-   `node-bound-to-policy`: Triggered after a node has been bound to a policy. The script input contains a `policy` property with the details of the policy that has been bound to the node.
-   `node-unbound-from-policy`: Triggered after a node has been marked as uninstalled by the `reinstall-node` command and thus has been returned to the set of nodes available for installation.
-   `node-facts-changed`: Triggered whenever a node changes its facts.
-   `node-install-finished`: Triggered when a policy finishes its last step.

