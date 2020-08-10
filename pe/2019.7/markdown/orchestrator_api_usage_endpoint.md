---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Puppet orchestrator API: usage endpoint

Use the `/usage` endpoint to view details about the active nodes in your deployment.

## GET /usage

List the daily node usage of the orchestrator and nodes that are present in PuppetDB.

### Parameters

-   **start\_date**

    The first day to include in the query. Format as YYYY-MM-DD.

-   **end\_date**

    The last day to include in the query. This date must be on or after the start date and formatted as YYYY-MM-DD.


### Response format

The response is a JSON object indicating the total number of active nodes and subtotals of the nodes with and without an agent installed.

The following keys are used:

-   **items**

    Contains an array of entries from most recent to least recent.

    -   **date**

        The date in ISO 8601 date format YYYY-MM-DD.

    -   **total\_nodes**

        The total number of nodes used on the given day.

    -   **nodes\_with\_agent**

        The number of active nodes in PuppetDB as of UTC midnight.

    -   **nodes\_without\_agent**

        The number of unique nodes that don't have an agent that were used since UTC midnight.

-   **pagination**

    An optional object that includes information about the original query.

    -   **start\_date**

        The starting day for the request \(if specified\).

    -   **end\_date**

        The last day requested \(if specified\).


```json
{
  "items":[
      {
        "date": "2018-06-08",
        "total_nodes": 100,
        "nodes_with_agent": 95,
        "nodes_without_agent": 5
      }, {
        "date": "2018-06-07",
        "total_nodes": 100,
        "nodes_with_agent": 95,
        "nodes_without_agent": 5
      }, {
        "date": "2018-06-06",
        "total_nodes": 100,
        "nodes_with_agent": 95,
        "nodes_without_agent": 5
      }, {
        "date": "2018-06-05",
        "total_nodes": 100,
        "nodes_with_agent": 95,
        "nodes_without_agent": 5
      }
  ],
  "pagination":{
     "start_date": "2018-06-01",
     "end_date": "2018-06-30"
  }
}
```

### Error Responses

For this endpoint, the `kind` key of the error displays the conflict.

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

