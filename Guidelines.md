# Guidelines for the Delivery API TODO

+ kratke intro o tom, co sa na tejto page nachadza, dovody za tym.
Welcome to our Delivery API guidelines page created to help you get all the information about our Delivery API in one place. This page contains all the technical limitations and restrictions of our Delivery API, error formats that we use, naming conventions and more.

The main idea behind this page was to make it easier for you in case you'd like to develop your own SDK using the Delivery API, as ...
x
All of this can be a great stepping stone when developing your own SDK while using the Delivery API. These guidelines will guide you through all the requirements needed to create a Delivery API based SDK and also some recommendations to better comply with what we already have and use in out own SDKs.

See also the Content Management API guidelines and its limitations.


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

0: "Unknown internal server error. Please contact our support."<br>
1: "No HTTP resource was found that matches the request. Please read API documentation at https://developer.kenticocloud.com ."<br>
2: null // only error code is used, the rest is filled from the HttpException itself<br>
3: "Missing or invalid access token. Please include the valid access token value in the Authorization header field as an HTTP bearer authorization scheme."<br>
4: "Access token does not provide the permissions required for the project."<br>

### Controller errors

100: "The requested content item '{0}' was not found."<br>
101: "The requested content type '{0}' was not found."<br>
102: "The requested content element '{0}' was not found in content type '{1}'."<br>
104: "The requested taxonomy group '{0}' was not found."<br>

### Query parser errors

1000: "The RANGE operator expects two parameters. {0} provided."<br>
1001: "Parameter 'elements' has to contain only names of elements, not its fields. Incorrect parameter name was '{0}'."<br>
1002: "Operator '{0}' was not recognized as a valid operator."<br>
1003: "Query parameter '{0}' must be specified only once."<br>
1004: "Parameter '{0}' is a reserved parameter and we didn't recognize the syntax. If you meant to access the element called '{0}', you need to prefix it with 'elements.'."<br>
1005: "Query parameter '{0}' must be a positive integer."<br>
1006: "Query parameter '{0}' must be a positive integer number or zero."<br>
1007: "You tried to use '{0}' in order parameter. The only valid values are 'ASC' and 'DESC'."<br>
1008: "It is not allowed to order by '{0}'. You can order by system properties or elements only. Elements must be prefixed with 'elements.'."<br>
1009: "Ordering by multiple parameters is not allowed."<br>
1010: "Query parameter '{0}' must have a value between {1} and {2}."<br>


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


## HTTP headers

### Request headers

Authorization - authorization of Secured Delivery API
* X-KC-Wait-For-Loading-New-Content - used usually when fetching fresh content based on a webhook call
* X-KC-SDKID - used internally/automatically for SDK version tracking

Webhooks - we add these to every webhook call:
* X-KC-Message-Id
* X-KC-Message-Type
* X-KC-Message-Operation
* X-KC-Message-Api-Name
* X-KC-Message-Project-Id
* X-KC-Message-Webhook-Url
* X-KC-Message-Created-Timestamp

### Response headers

The response headers are mostly standard or not really useful for the customer (the exception may be X-Cache and X-Cache-Hits).

**Standard**
* Accept-Ranges
* Access-Control-Allow-Origin
* Age
* Connection
* Content-Encoding
* Content-Length
* Content-Type
* Date
* Expires
* Pragma
* Request-Context
* Server
* Vary
* Via

**Proprietary**
* X-AspNet-Version
* X-Cache - possible values: MISS, HIT - indicates if the response comes from Fastly cache
* X-Cache-Hits - integer - indicates how many times the response came from Fastly cache so far
* X-Powered-By
* X-Served-By
* X-Timer


## Expected functionality

+ Use our feature matrix as a lead -> to explain what should be covered by the SDK. f.e. https://developer.kenticocloud.com/docs/net

### List of required endpoints:

GET List content items https://deliver.kenticocloud.com/project_id/items
GET View a content item https://deliver.kenticocloud.com/project_id/items/content_item_codename
GET List content types https://deliver.kenticocloud.com/project_id/types
GET View a content type https://deliver.kenticocloud.com/project_id/types/content_type_codename
GET View a content type element https://deliver.kenticocloud.com/project_id/types/content_type_codename/elements/element_codename
GET List taxonomy groups https://deliver.kenticocloud.com/project_id/taxonomies
GET View a taxonomy group https://deliver.kenticocloud.com/project_id/taxonomies/taxonomy_group_codename

### Strong vs. weak typing

If supported by a given stack, prefer strong typing (community can implement untyped support, if required)
If implemented, untyped methods should return raw data (=> should not modify content...e.g. don't resolve links)

+  suggestions how to use ExternalID with upsert to allow repeatable imports  
+ continuationToken usage
+ non-updatable props (Codename, LastModified, ExternalID, Item.Type, Variant.Language...) - updates are not leading to error but values are simply ignored
+ RichText forma


## Naming conventions

**Namespaces**

Main namespace: Kentico Cloud

Sub namespace: Name of the endpoint (e.g. Personalization)

Namespace separator: as per target platform

Letter case: as per target platform

**Project name (GH repo name)**

[Main namespace] <Sub Namespace> <Platform> [Paradigm] SDK
Platform: php, java, swift (not iphone)

Paradigm: Rx (reactive), MVC

E.g. PersonalizationJavaRxSDK

If the project is not hosted under Kentico's organization, the name should contain Kentico Cloud.

**Package names**

<Main namespace><Namespace separator><Sub Namespace>
  
## Other requirements  

+ we have an internal list of requirements that an OS project needs to comply with.
