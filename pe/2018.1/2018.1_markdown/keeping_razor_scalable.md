---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Keeping Razor scalable

Speed provisioning and reduce load on the Razor server by following these scalability best practices.

-   When [creating repos](repositories.md), use `url` rather than `iso-url`. If you have a mirror hosting installation files, they can be distributed straight to the node rather than having the Razor server distribute the files.
-   When [matching policies](policies.md), limit the number of tags you use.
    -   Create tags using the `has_macaddress` operator to identify a list of nodes to be assigned the tag, for example, `["has_macaddress", "de:ea:db:ee:f0:00", "de:ea:db:ee:f0:01"]`.
    -   Set the `policy` metadata key on nodes using the `modify-node-metadata` command, then create matching tags, for example, `["=", ["metadata", "policy"], "policy1"]`.
-   When [running hooks](hooks.md#), avoid long- and frequent-running scripts, which all run on the same thread synchronously.

