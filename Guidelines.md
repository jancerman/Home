# Guidelines for the Delivery API

## API Limitations

### Rate limits

As far as the Delivery API goes, there is no rate limitation. This means we do not specify the number of requests you can make to the Delivery API within a specific time window.

### Data type limits

In the following table, you can see all of our content type elements, their data type and the limitations.

<table>
<tbody>
<tr>
<th><h3>Content type element</h3></th><th><h3>Data type</h3></th><th><h3>(Accepted) Limits</h3></th>
</tr>
<tr>
<td>Text</td><td>string</td><td>hard limit of 100,000 characters</td>
</tr>
<tr>
<td>Rich text</td><td>string</td><td>Hard limit of 100,000 characters. NOTE: We are counting markup and whitespaces as well.</td>
</tr>
<tr>
<td>Multiple choice</td><td>string</td><td>can contain up to 250 predefined options</td>
</tr>
<tr>
<td>Number</td><td>decimal number</td><td>"##########.##########" 10 numbers before and after the decimal separator</td>
</tr>
<tr>
<td>Date & time</td><td>string</td><td>Valid format: YYYY-MM-DDTHH:MM:SSZ. ISO-8601 formatted string.</td>
</tr>
<tr>
<td>Asset</td><td>image</td><td>100 MB</td>
</tr>
<tr>
<td>Modular content</td><td>string</td><td> ? </td>
</tr>
<tr>
<td>URL slug</td><td>string</td><td>limit of 100,000 characters</td>
</tr>
<tr>
<td>Guidelines</td><td>string</td><td>limit of 10,000 characters</td>
</tr>
</tbody>
</table>

+ how about Content type snippet? Taxonomies?

### Content element limits

There are a few more limitations that you should keep in mind for the content element itself.

<table>
<tbody>
<tr>
<th><h3>Element field</h3></th><th><h3>Data type</h3></th><th><h3>Limits</h3></th>
</tr>
<tr>
<td>Label</td><td>string</td><td>limit of 50 characters</td>
</tr>
<tr>
<td>Guidelines</td><td>string</td><td>limit of 10,000 characters</td>
</tr>

### Request limits

All in all, the maximum request length can go up to 2097151 kB (2GB).


## API Error codes

In this section, we summed up the possible error codes that you can receive when working with the Delivery API.

Retry policy: Only errors with codes 0 and 2 is sensible to retry. -> explain further?

### Error format specification

```
{
  "message": "Query parameter 'limit' must be a positive integer.", // Clear specification of what happened.
  "request_id": "GMAQvDLeLbE=", 
  "error_code": 1005,
  "specific_code": 0
}
```

### General errors

0: "Unknown internal server error. Please contact our support."
1: "No HTTP resource was found that matches the request. Please read API documentation at https://developer.kenticocloud.com ."
2: null // only error code is used, the rest is filled from the HttpException itself
3: "Missing or invalid access token. Please include the valid access token value in the Authorization header field as an HTTP bearer authorization scheme."
4: "Access token does not provide the permissions required for the project."

### Controller errors

100: "The requested content item '{0}' was not found."
101: "The requested content type '{0}' was not found."
102: "The requested content element '{0}' was not found in content type '{1}'."
104: "The requested taxonomy group '{0}' was not found."

### Query parser errors

1000: "The RANGE operator expects two parameters. {0} provided."
1001: "Parameter 'elements' has to contain only names of elements, not its fields. Incorrect parameter name was '{0}'."
1002: "Operator '{0}' was not recognized as a valid operator."
1003: "Query parameter '{0}' must be specified only once."
1004: "Parameter '{0}' is a reserved parameter and we didn't recognize the syntax. If you meant to access the element called '{0}', you need to prefix it with 'elements.'."
1005: "Query parameter '{0}' must be a positive integer."
1006: "Query parameter '{0}' must be a positive integer number or zero."
1007: "You tried to use '{0}' in order parameter. The only valid values are 'ASC' and 'DESC'."
1008: "It is not allowed to order by '{0}'. You can order by system properties or elements only. Elements must be prefixed with 'elements.'."
1009: "Ordering by multiple parameters is not allowed."
1010: "Query parameter '{0}' must have a value between {1} and {2}."


## Returned data

Application/json is returned for every route.

### Object model descriptions of the listing responses

In this section, we described the object model descriptions of the listing responses (for content items, content types, and taxonomies) returned by the Delivery API. When you retrieve a list of content items, content types or taxonomy groups from your project, the Delivery API returns a paginated listing response. 

**Content items listing response**

<table>
<tbody>
<tr>
<th><h3>Attribute</h3></th><th><h3>Description</h3></th><th><h3>Type</h3></th>
</tr>
<tr>
<td>items</td><td>A list of content items</td><td>array of Content item objects</td>
</tr>
<tr>
<td>modular_content</td><td>A list of content items used in Modular content and Rich text elements</td><td>collection of Content item objects</td>
</tr>
<tr>
<td>pagination</td><td>Information about the retrieved page</td><td>Pagination</td>
</tr>
</tbody>
<table>

**Content types listing response**

<table>
<tbody>
<tr>
<th><h3>Attribute</h3></th><th><h3>Description</h3></th><th><h3>Type</h3></th>
</tr>
<tr>
<td>types</td><td>A list of content types</td><td>array of Content type objects</td>
</tr>
<tr>
<td>pagination</td><td>Information about the retrieved page</td><td>Pagination</td>
</tr>
</tbody>
<table>

**Taxonomy listing response**

<table>
<tbody>
<tr>
<th><h3>Attribute</h3></th><th><h3>Description</h3></th><th><h3>Type</h3></th>
</tr>
<tr>
<td>taxonomies</td><td>A list of taxonomy groups</td><td>array of Taxonomy group objects</td>
</tr>
<tr>
<td>pagination</td><td>Information about the retrieved page</td><td>Pagination</td>
</tr>
</tbody>
<table>

**Pagination object**

<table>
<tbody>
<tr>
<th><h3>Attribute</h3></th><th><h3>Description</h3></th><th><h3>Type</h3></th><th><h3>Notes</h3></th>
</tr>
<tr>
<td>skip</td><td>Number of content items skipped from the response</td><td>integer</td><td>Reflects the value set by the skip query parameter.</td>
</tr>
<tr>
<td>limit</td><td>Number of content items returned in the response</td><td>integer</td><td>Reflects the value set by the limit query parameter.</td>
</tr>
<tr>
<td>count</td><td>Number of retrieved content items</td><td>integer</td><td>If the limit and skip query parameters aren't set, the count attribute will contain the total number of content items matching the specified filtering parameters.</td>
</tr>
<tr>
<td>next_page</td><td>URL to the next page of results</td><td>string</td><td>...</td>
</tr>
</tbody>
<table>

+ data types are already described in the "Data type limits" table, should I still explicitely mention this?


### Modular content in the response

+ supported levels of nesting ?

<table>
<tbody>
<tr>
<th><h3>Element</h3></th><th><h3>Supported tags</h3></th>
</tr>
<tr>
<td>headings</td><td>h1, h2, h3, h4</td>
</tr>
<tr>
<td>text</td><td>p, strong, em</td>
</tr>
<tr>
<td>ordered list</td><td>ol, li</td>
</tr>
<tr>
<td>unordered list</td><td>ul, uli</td>
</tr>
<tr>
<td>table</td><td>table, tbody, tr, td</td>
</tr>
<tr>
<td>web links</td><td>a tag with href</td>
</tr>
<tr>
<td>content item link</td><td>a tag with data-item-id</td>
</tr>
<tr>
<td>asset link</td><td>a tag with data-asset-id</td>
</tr><tr>
<td>inline image</td><td>figure tag with data-asset-id and image tag</td>
</tr>
<tr>
<td>content module</td><td>object tag with type="application/kenticocloud", data-type="item" and data-item-id</td>
</tr>
</tbody>
<table>

### Empty response vs. 404

We differentiate between the correct query which doesn't bring any results and returns 200 error code and the wrong query which causes a 404 error. 

Empty responses when querying non-existent data -> GET all /items , GET all /types, GET all /taxonomies endpoints.


