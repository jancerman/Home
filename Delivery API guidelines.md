# Guidelines for SDK developers

Welcome to the Kentico Cloud guidelines for SDK developers! These guidelines cover all the requirements needed to create an SDK for the [Delivery API](https://developer.kenticocloud.com/reference#delivery-api). Also, we've added recommendations on naming conventions to better match naming used in our existing SDKs. This should give you a solid starting point for developing your own SDKs in the framework of your choice.

The guidelines cover the following aspects:

* Expected SDK functionality
* Format of the returned data
* Rate limits
* Errors
* HTTP headers

## Expected functionality

### Supported endpoints

The SDK must support retrieving content from the following Delivery API endpoints:

Method | Endpoint | Title
---------|----------|---------
GET | https://deliver.kenticocloud.com/{project_id}/items | List content items
GET | https://deliver.kenticocloud.com/{project_id}/items/{content_item_codename} | View a content item
GET | https://deliver.kenticocloud.com/{project_id}/types | List content types
GET | https://deliver.kenticocloud.com/{project_id}/types/{content_type_codename} | View a content type
GET | https://deliver.kenticocloud.com/{project_id}/types/{content_type_codename}/elements/{element_codename} | View a content type element 
GET | https://deliver.kenticocloud.com/{project_id}/taxonomies | List taxonomy groups
GET | https://deliver.kenticocloud.com/{project_id}/taxonomies/{taxonomy_group_codename} | View a taxonomy group

### Supported features

The SDK should also support the following features:

Feature | Explanation
---------|----------
Delivery Preview API | Support for [previewing unpublished content items](https://developer.kenticocloud.com/reference#section-production-vs-preview) via the Delivery Preview API.
Content filtering | Support for the [filtering parameters](https://developer.kenticocloud.com/reference#content-filtering) when making queries to the Delivery API.
Localization | Support for retrieving localized content, that is using the `language` query parameter when retrieving content items.
Rendering Rich text elements | Support for rendering content of the built-in Rich text elements. This content includes links, images, components, and other content items.
Resolving links | Support for resolving hypertext links to other content items within the built-in Rich text elements.
Retrieving linked items | Support for retrieving content items and components that are used within other content items.

### Strong vs. weak typing

We recommend that you use strong typing for the SDK, if supported by a given stack. Note that the community can implement untyped support later, if needed. In such case, the untyped methods should return raw data from the Delivery API and should not modify the content in any way.

For information about content elements and their data types, see [Data types](#data-types) below.

## Returned data

The Delivery API returns JSON payloads both for successful and [unsuccessful](#errors) requests.

### Object model descriptions of the returned data

When you query endpoints for retrieving all `/items`, `/types`, or `/taxonomies`, the Delivery API returns a paginated listing response with content that matches your specific query. In each case, the listing response contains a pagination object with information about the number of retrieved items, a link to the next page of results.

You can find [descriptions and examples of the listing responses](https://developer.kenticocloud.com/reference#listing-response) for content items, content types, and taxonomy groups in our API reference.

For details on the structure of the objects returned by the Delivery API, see the these pages in the API reference:

* [Content item model](https://developer.kenticocloud.com/reference#content-item-object)
* [Content type model](https://developer.kenticocloud.com/reference#content-type-object)
* [Taxonomy group model](https://developer.kenticocloud.com/reference#taxonomy-group-object)

### Data types

Content element | Data type | Limitation
--- | --- | ---
Text | string | Hard limit of 100,000 characters.
Rich text | string | Hard limit of 100,000 characters. **Note**: The limit includes HTML markup and whitespace characters.
Multiple choice | string | Can contain up to 250 predefined options.
Number | decimal number | Numbers have the following format: "##########.##########" 10 numbers before and after the decimal separator
Date & time | string | Valid format: YYYY-MM-DDTHH:MM:SSZ. ISO-8601 formatted string.
Asset | image | 100 MB
Modular content | string | None.
URL slug | string | Hard limit of 100,000 characters.

You can find [example JSON values for each of the content elements](https://developer.kenticocloud.com/reference#content-type-element-object) in our API reference.

### Empty response vs. 404 error

The Delivery API differentiates between a correct query, which doesn't bring any results and gives a 200 OK response, and a wrong query, which causes a 404 Not found response.

For example, you can get an empty response when querying [endpoints](#supported-endpoints) for `/items`, `/types`, or `/taxonomies`.

### Errors

The Delivery API returns errors in the JSON format. Here's an example of an error message:

```json
{
  "message": "No HTTP resource was found that matches the request. Please read API documentation at https://developer.kenticocloud.com.",
  "request_id": "|38a7b46fc8597a4aa1ab0169449cac54.c37263a1_",
  "error_code": 1,
  "specific_code": 0
}
```

The `message` property in the error message provides a clear specification of what went wrong when making the request.

You can distinguish between the different error messages by their `error_code` property, if needed. Single digit numbers signify general errors, `1XX` numbers signify errors occurring when a resource was not found, and `1XXX` numbers signify query parser errors.

Here are some examples of the general errors:

* `0`: "Unknown internal server error. Please contact our support."
* `1`: "No HTTP resource was found that matches the request. Please read API documentation at https://developer.kenticocloud.com."
* `2`: null
* `3`: "Missing or invalid access token. Please include the valid access token value in the Authorization header field as an HTTP bearer authorization scheme."
* `4`: "Access token does not provide the permissions required for the project."

## HTTP headers

### Analytics

We strongly recommend that the SDK sends a `X-KC-SDKID` header with each request to the API. The value of the header should be formatted like this `<SDK package origin>;<SDK name>;<SDK version>`. For example, for the version 9 of the [Delivery .NET SDK](https://github.com/Kentico/delivery-sdk-net), the header value would be `nuget.org;KenticoCloud.Delivery;9.0.0`.

### Requesting latest content

By default, the Delivery API serves old content (if cached by the Fastly CDN) while it's fetching the new content to minimize wait time. In some cases, such as [reacting to webhook notifications](https://developer.kenticocloud.com/docs/webhooks), you might want to explicitly request the latest content from Kentico Cloud.

To do this within your SDK, include the `X-KC-Wait-For-Loading-New-Content` header and set it to `true` when sending requests to the API.

Including the header will cause the Delivery API to wait while fetching new content.

## API limits

### Rate limitation

There is no rate limitation for the Delivery API. This means that we don't specify any maximum number of requests you can make to the API within a specific time window.

### URL length

The length of URLs for the GET requests is limited to 2048 characters. The Delivery API returns an [error](#errors) for URLs longer than 2048 characters.

### Request length

The length of a GET request to the Delivery API is not limit. Technically, the requests may be up to 2097151 kB (2 GB) long.

## Naming conventions

This parts contains recommended naming conventions for the project name, the namespaces in your project, and package names.

### Namespaces

* Main namespace: Kentico Cloud
* Sub namespace: Name of the API (e.g., Delivery)
* Namespace separator: as per target platform
* Letter case: as per target platform

### Project name (GitHub repo name)

We recommend that the project for your SDK uses the following naming pattern: [Main namespace] <Sub Namespace> <Platform> [Paradigm] SDK. For example, for the Delivery .NET SDK, the project name would be Kentico Cloud Delivery .NET SDK.

## Other requirements

For your SDK to be included within the list Kentico Cloud SDKs, the SDK project has to comply with our checklist for [Publishing an OS project].
