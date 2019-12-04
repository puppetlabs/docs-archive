<!--APIs are a specific type of reference. Each topic (H2) documents one endpoint, with sections (H3) describing the request format, response format, error responses, and any additional information.-->

## <# TITLE -- Insert the name of the endpoint, for example `GET /v1/group-children/:id` #>

<# SHORTDESC -- Insert a 1-2 sentence description of the endpoint's purpose. #>

### Request format

<# Insert details about the request format. #>

<!--Use a table to document keys and definitions. Simple HTML tables are useful for straightforward data. Use HTML tables for data that requires formatting, like line breaks or bulleted lists within cells. Avoid adding more than 5 columns to a single table.-->

<# Key #>   | <# Definition #>   |
----------------|:---------------:|
<# cell data #> | <# cell data #> |
<# cell data #> | <# cell data #> |

<!--Use a code block to document an example request.-->

<#
```

```
#>

### Response format

<# Insert a description of a successful response. #>

<table>
  <tr>
    <th><# Key #></th>
    <th><# Definition #></th>
  </tr>
  <tr>
    <td><# cell data #></td>
    <td><# cell data #></td>
  </tr>
  <tr>
    <td><# cell data #></td>
    <td><p><#first line #></p> <p><# second line, introducing a list: #>
     <ul>
      <li><# list item #></li>
      <li><# list item #></li>
     </ul></p>
    </td>
  </tr>
</table>

<!--Use a code block to document an example response.-->

<#
```

```
#>

### Error responses

<# Insert a description of possible error responses. If there are no error responses for the endpoint, state "There are no error responses expected for this operation." #>

<# Error #>   | <# Definition #>   |
----------------|:---------------:|
<# cell data #> | <# cell data #> |
<# cell data #> | <# cell data #> |


### <# SECTION (optional) -- If the endpoint has additional details, such as parameters or permissions, insert more sections as needed. #>

* * *