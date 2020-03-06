---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Classification endpoint

The `classification` endpoint takes a node name and a set of facts, and returns information about how that node is classified. The output can help you test your classification rules.

-   **[POST /v2/classified/nodes/<name\>](post_v2_classified_nodes_name.md)**  
Use the `/v2/classified/nodes/\<name\>` endpoint to retrieve the classification information for the node with the given name and facts as supplied in the body of the request.

