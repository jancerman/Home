# Guidelines for the Content Management API 

Welcome to our Content Management API (CM API) guidelines page created to help you get all the information about our CM API in one place. This page contains all the technical limitations and restrictions of our CM API, error formats that we use, naming conventions and more.

See also the <add link>Delivery API guidelines and its limitations.


## API Limitations

### Rate limits

Rate limits specify the number of requests you or your application can make to the Content Management API within a specific time window. There are three separate time windows (second, minute, hour) allowing a different number of requests each.

By default, the API enforces rate limits of 10 requests per second, 400 requests per minute, and 15000 requests per hour. These limits apply for each user token, i.e., the API key for the Content Management API.

When you reach the limit of a specific time window, the API will reject the request and respond with the `429` HTTP error. The response will include the Retry-After header that tells you how many seconds you need to wait before attempting further requests. Each failed request is perfectly safe to retry.

If you begin to receive `429` HTTP errors, reduce the frequency of your requests.

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

Content item name is limited to 50 chars.

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
</tbody>
</table>

### Request limits

All in all, the maximum request length can go up to 2097151 kB (2GB).


## API Error codes

In this section, we summed up the possible error codes that you can receive when working with the CM API.

### Error format specification

```
{
    "request_id": "4174eb06-fc7b-4a60-ab74-7d4463151729",
    "error_code": 5,
    "message": "The provided request body is invalid. See the 'validation_errors' attribute for more information and specify a valid JSON object.", 
    // "validation_errors" is ommited for errors which are not caused by invalid request JSON
    "validation_errors": [
        {
            "message": "The reference must be an object containing exactly one identifier - id, codename or external_id.", // Clear explanation of what happened.
            "path": "type",
            "line": 3,
            "position": 19
        }
    ]
}
```

### General errors (and others)

All the error codes are in this file: https://github.com/Kentico/draft-backend/blob/master/API/ContentManagementApi/Errors/ManageApiErrors.cs

+ Doplnit podobne ako to je v Delivery API

### Retry policy

Only the InternalServerError (`0`) and RateLimitationExceeded (`10000`) are worth retrying.

+ other HTTP errors which are generally worth retrying:

HttpStatusCode.RequestTimeout, // 408
HttpStatusCode.InternalServerError, // 500
HttpStatusCode.BadGateway, // 502
HttpStatusCode.ServiceUnavailable, // 503
HttpStatusCode.GatewayTimeout // 504


## Returned data

What you should know about the data that the CM API returns:

* Application/json is returned for every route.
* Types of returned data: number/string/arrays/objects (see API reference). -> these are already described in "Data type limits" table
* Read (GET) endpoints can return empty arrays. Note: We are not returning 404 for empty projects.

### Object model descriptions of the listing responses

In this section, we described the object model descriptions of the listing responses (for content items and assets) returned by the Content Management API. When you request a list of content items or assets, the Content management API returns a dynamically paginated listing response limited to up to 100 objects, i.e., content items or assets, per one page.

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
<td>pagination</td><td>Information about the retrieved page</td><td>Pagination</td>
</tr>
</tbody>
<table>

**Asset listing response**

<table>
<tbody>
<tr>
<th><h3>Attribute</h3></th><th><h3>Description</h3></th><th><h3>Type</h3></th>
</tr>
<tr>
<td>assets</td><td>A list of assets</td><td>array of Asset objects</td>
</tr>
<tr>
<td>pagination</td><td>Information about the next page of results</td><td>Pagination</td>
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
<td>continuation_token</td><td>dentifier of the next page of results</td><td>string</td><td>Is `null` when on the last page of results.</td>
</tr>
<tr>
<td>next_page</td><td>URL to the next page of results</td><td>string</td><td>Is `null` when on the last page of results.</td>
</tr>
</tbody>
<table>

+ There is no specification for items count returned in the one batch, developer should iterate using continuation_token to load all items


## HTTP headers

### Request headers

Authorization Bearer <API_KEY>
* X-KC-Wait-For-Loading-New-Content - used usually when fetching fresh content based on a webhook call
* X-KC-SDKID - used internally/automatically for SDK version tracking

+ Add note for the webhooks support!

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

### List of required endpoints

+ SDK should cover every endpoint specified in the API ref.

Content model

<table>
<tbody>
<tr>
  <th><h3>Method</h3></th><th><h3></h3></th><th><h3>Endpoint</h3></th>
</tr>
<tr>
  <td>POST</td><td>Project content</td><td>Validate project content https://developer.kenticocloud.com/v1/reference#content-management-api-validate-project</td>
</tr>
<tr>
  <td>POST</td><td>Content item</td><td>Add a content item https://developer.kenticocloud.com/v1/reference#content-management-api-add-item</td>
</tr>
<tr>
  <td>GET</td><td>Content items</td><td>List content items https://developer.kenticocloud.com/v1/reference#content-management-api-list-items</td>
</tr>
<tr>
  <td>PUT</td><td>Content item by ID</td><td>Update a content item by ID https://developer.kenticocloud.com/v1/reference#content-management-api-item-by-id</td>
</tr>
<tr>
  <td>PUT</td><td>Content item by ID</td><td>View a content item by ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-item-by-item-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Content item by ID</td><td>Delete a content item by ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-item-by-item-id</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by ID</td><td>List language variants https://developer.kenticocloud.com/v1/reference#content-management-api-list-variants-by-item-id</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by ID</td><td>Upsert a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-id-language-id</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by ID</td><td>Upsert a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-id-language-codename</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by ID</td><td>View a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-id-language-id</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by ID</td><td>View a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-id-language-codename</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by ID</td><td>Delete a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-id-language-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by ID</td><td>Delete a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-id-language-codename</td>
</tr>
<tr>
  <td>PUT</td><td>Content item by codename</td><td>Update a content item by codename https://developer.kenticocloud.com/v1/reference#content-management-api-update-item-by-item-codename</td>
</tr>
<tr>
  <td>GET</td><td>Content item by codename</td><td>View a content item by codename https://developer.kenticocloud.com/v1/reference#content-management-api-view-item-by-item-codename</td>
</tr>
<tr>
  <td>DELETE</td><td>Content item by codename</td><td>Delete a content item by codename https://developer.kenticocloud.com/v1/reference#content-management-api-delete-item-by-item-codename</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by codename</td><td>List language variants https://developer.kenticocloud.com/v1/reference#content-management-api-list-variants-by-item-codename</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by codename</td><td>Upsert a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-codename-language-id</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by codename</td><td>Upsert a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-codename-language-id</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by codename</td><td>View a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-codename-language-id</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by codename</td><td>View a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-codename-language-codename</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by codename</td><td>Delete a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-codename-language-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by codename</td><td>Delete a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-codename-language-codename</td>
</tr>

<tr>
  <td>PUT</td><td>Content item by external ID</td><td>Upsert a content item by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-item-by-item-external-id</td>
</tr>
<tr>
  <td>GET</td><td>Content item by external ID</td><td>View a content item by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-item-by-external-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Content item by external ID</td><td>Delete a content item by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-item-by-item-external-id</td>
</tr>

<tr>
  <td>GET</td><td>Language variant by external ID</td><td>List language variants https://developer.kenticocloud.com/v1/reference#content-management-api-list-variants-by-item-external-id</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by external ID</td><td>Upsert a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-external-id-language-id</td>
</tr>
<tr>
  <td>PUT</td><td>Language variant by external ID</td><td>Upsert a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-variant-by-item-external-id-language-codename</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by external ID</td><td>View a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-external-id-language-id</td>
</tr>
<tr>
  <td>GET</td><td>Language variant by external ID</td><td>View a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-view-variant-by-item-external-id-language-codename</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by external ID</td><td>Delete a variant by language ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-external-id-language-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Language variant by external ID</td><td>Delete a variant by language codename https://developer.kenticocloud.com/v1/reference#content-management-api-delete-variant-by-item-external-id-language-codename</td>
</tr>
</tbody>
</table>

Asset model 

<table>
<tbody>
<tr>
  <th><h3>Method</h3></th><th><h3> </h3></th><th><h3>Endpoint</h3></th>
</tr>
<tr>
  <td>POST</td><td></td><td>Upload a binary file https://developer.kenticocloud.com/v1/reference#content-management-api-upload-file</td>
</tr>
<tr>
  <td>POST</td><td></td><td>Add an asset https://developer.kenticocloud.com/v1/reference#content-management-api-add-asset</td>
</tr>
<tr>
  <td>GET</td><td></td><td>List assets https://developer.kenticocloud.com/v1/reference#content-management-api-list-assets</td>
</tr>
    
<tr>
  <td>PUT</td><td>Asset by ID</td><td>Update an asset by ID https://developer.kenticocloud.com/v1/reference#content-management-api-list-assets</td>
</tr>
<tr>
  <td>GET</td><td>Asset by ID</td><td>View an asset by ID https://developer.kenticocloud.com/v1/reference#content-management-api-list-assets</td>
</tr>
<tr>
  <td>DELETE</td><td>Asset by ID</td><td>Delete an asset by ID https://developer.kenticocloud.com/v1/reference#content-management-api-list-assets</td>
</tr>

<tr>
  <td>PUT</td><td>Asset by external ID</td><td>Upsert an asset by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-upsert-asset-by-external-id</td>
</tr>
<tr>
  <td>GET</td><td>Asset by external ID</td><td>View an asset by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-view-asset-by-external-id</td>
</tr>
<tr>
  <td>DELETE</td><td>Asset by external ID</td><td>Delete an asset by external ID https://developer.kenticocloud.com/v1/reference#content-management-api-delete-asset-by-external-id</td>
</tr>

</tbody>
</table>

### External ID usage

+ developer should know benefits of using external_id (https://developer.kenticocloud.com/v1/docs/importing-modular-content) and allow users to reference items by id/codename/external_id
+ suggestions how to use ExternalID with upsert to allow repeatable imports  

### Non-updatable properties

Some properties which are included in the response models are not updatable. These props can be included in the request model but their values will be ignored (same rule applies to unknown props - API just ignores them)

Content item - ID, Codename (generated by api), Type, LastModified, ExternalId

Content item variant - Item, Language, LastModified

Asset - ExternalId, Id, FileName, Type, LastModified

Note that ommited props in the update models are are not updated => in case developer want to clear the value he needs to send "prop_name" : null in the model.

+ non-updatable props (Codename, LastModified, ExternalID, Item.Type, Variant.Language...) - updates are not leading to error but values are simply ignored

### Other

+ continuationToken usage
+ RichText format


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
