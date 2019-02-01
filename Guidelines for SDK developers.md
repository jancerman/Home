# Guidelines for SDK developers

Welcome to the Kentico Cloud guidelines for SDK developers! These guidelines cover all the requirements needed to create SDKs for the [Kentico Cloud Delivery API](https://developer.kenticocloud.com/reference#delivery-api) and should give you a solid starting point for developing SDKs in the framework of your choice.

The guidelines cover the following topics:

* [Expected SDK functionality](#expected-functionality)
* [Ensuring backward compatibility](#ensuring-backward-compatibility)
* [Format of the returned data](#returned-data)
* [Sending HTTP headers](#sending-http-headers)
* [Delivery API limits](#delivery-api-limits)
* [Naming conventions](#naming-conventions)
* [OS project requirements](#os-project-requirements)

## Expected functionality

### Required endpoints

The SDK must support retrieving content from the following Delivery API endpoints:

Method | Endpoint
---------|----------
GET | List content items <br/> `https://deliver.kenticocloud.com/{project_id}/items`
GET | View a content item <br/> `https://deliver.kenticocloud.com/{project_id}/items/{item_codename}`
GET | List content types <br/> `https://deliver.kenticocloud.com/{project_id}/types`
GET | View a content type <br/> `https://deliver.kenticocloud.com/{project_id}/types/{type_codename}`
GET | View a content type element <br/> `https://deliver.kenticocloud.com/{project_id}/types/{type_codename}/elements/{element_codename}`
GET | List taxonomy groups <br/> `https://deliver.kenticocloud.com/{project_id}/taxonomies`
GET | View a taxonomy group <br/> `https://deliver.kenticocloud.com/{project_id}/taxonomies/{taxonomy_group_codename}`

### Feature support

The SDK should also support the following features:

Feature | Description
---------|----------
Delivery Preview API | Support for [previewing unpublished content items](https://developer.kenticocloud.com/reference#section-production-vs-preview) via the Delivery Preview API.
Content filtering | Support for the [filtering parameters](https://developer.kenticocloud.com/reference#content-filtering) when making queries to the Delivery API.
Localization | Support for retrieving localized content, that is using the `language` query parameter when retrieving content items.
Rendering Rich text elements | Support for rendering content of the built-in Rich text elements. This content includes links, images, components, and other content items.
Resolving links | Support for resolving hypertext links to other content items within the built-in Rich text elements.
Retrieving linked items | Support for retrieving content items and components that are used within other content items.

### Strong vs. weak typing

We recommend that you use strong typing for the SDK, if supported by a given stack. Note that the community can implement untyped support later, if needed. In such case, the untyped methods should return raw data from the Delivery API and should not modify the content in any way.

For information about content elements and their data types, see [Content element data types](#content-element-data-types) below.

## Ensuring backward compatibility

The Delivery API evolves in a way that is backward compatible. This means that, as long as you follow a few rules, changes in the Delivery API should not break client applications. Even when the API changes, you can ensure that applications using your SDK still work correctly with the current API version.

The following changes to the Delivery API are **NOT** considered breaking changes and you should ignore them in your SDK.

* Add a new property to JSON objects.
* Change the order of JSON object properties (with exceptions, see below).
* Add a new type of content element.
* Add a new HTML element to the Rich text elements.
* Add a new attribute to HTML element in the Rich text elements.
* Add a new value to HTML element attribute in the Rich text elements.
* Change the order of HTML element attributes in the Rich text elements.
* Represent some characters as HTML entities.

**Note**: If a breaking change should happen, we will provide a new version of the Delivery API (alongside the current version of the API) together with new SDK guidelines. We will let you know in time before this happens.

You can always rely on the following rules:

* Elements in content types are in the same order as in Kentico Cloud UI.
* Elements in content item variants are in the same order as in content types.
* Options in Multiple choice elements in content types are in the same order as in Kentico Cloud UI.
* Selected options in Multiple choice elements in content item variants are in the same order as in content types.

To make your SDK more robust, we recommend also applying these guidelines:

* Ignore unknown content elements.
* Parse HTML with HTML 5 parses. If possible, do not use regular expressions.
* Ignore unknown HTML elements – keep their content, but ignore their opening and closing tags.

## Returned data

The Delivery API returns data in the JSON format both for successful and [unsuccessful](#errors) requests.

### Object model descriptions of the returned data

When you query endpoints for retrieving all `/items`, `/types`, or `/taxonomies`, the Delivery API returns a paginated listing response with content that matches your specific query. In each case, the listing response contains a pagination object with information about the number of retrieved items, and a link to the next page of results.

You can find [descriptions and examples of the listing responses](https://developer.kenticocloud.com/reference#listing-response) for content items, content types, and taxonomy groups in our API reference.

For details on the structure of the individual objects returned by the Delivery API (such as content items, content types, and taxonomy groups), see the these pages in the API reference:

* [Content item model](https://developer.kenticocloud.com/reference#content-item-object)
* [Content type model](https://developer.kenticocloud.com/reference#content-type-object)
* [Taxonomy group model](https://developer.kenticocloud.com/reference#taxonomy-group-object)

### Content element data types

Content element | Data type | Limitation
--- | --- | ---
Text | string | Hard limit of 100,000 characters.
Rich text | string | Hard limit of 100,000 characters. **Note**: The limit includes HTML markup and whitespace characters.
Multiple choice | string | Can contain up to 250 predefined options.
Number | decimal number | Numbers have the following format: "##########.##########" 10 numbers before and after the decimal separator
Date & time | string | Valid format: YYYY-MM-DDTHH:MM:SSZ. [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) formatted string.
Asset | binary | No limitation.
Modular content | string | No limitation.
URL slug | string | Hard limit of 100,000 characters.

You can find [example JSON values for each of the content elements](https://developer.kenticocloud.com/reference#content-type-element-object) in our API reference.

### Empty response vs. 404 error

The Delivery API differentiates between a correct query, which doesn't bring any results and gives a 200 OK response, and a wrong query, which causes a 404 Not found response.

For example, you can get an empty response when querying [endpoints](#required-endpoints) for `/items`, `/types`, or `/taxonomies` if no content matches your query. An example of a wrong query would be requesting a specific content item (using `/items/{item_codename}`) that doesn't exist.

### Errors

The Delivery API returns errors in the JSON format. Every error message has the same format. Here's an example of a general error message:

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

## Sending HTTP headers

### Analytics

We strongly recommend that the SDK sends a `X-KC-SDKID` header with each request to the API. The value of the header should be formatted like this `<SDK package origin>;<SDK name>;<SDK version>`. For example, for the version 9 of the [Delivery .NET SDK](https://github.com/Kentico/delivery-sdk-net), the header value would be `nuget.org;KenticoCloud.Delivery;9.0.0`.

### Requesting latest content

By default, the Delivery API serves old content (if cached by the Fastly CDN) while it's fetching the new content to minimize wait time. In some cases, such as [reacting to webhook notifications](https://developer.kenticocloud.com/docs/webhooks), you might want to explicitly request the latest content from Kentico Cloud.

To do this within your SDK, include the `X-KC-Wait-For-Loading-New-Content` header and set it to `true` when sending requests to the API.

Including the header will cause the Delivery API to wait while fetching new content.

## Delivery API limits

### Rate limitation

There is no rate limitation for the Delivery API. This means that we don't specify any maximum number of requests you can make to the API within a specific time window.

### URL length

The length of URLs for the GET requests is limited to 2048 characters. The Delivery API returns an [error](#errors) for URLs longer than 2048 characters.

## Naming conventions

Here are some recommendations on naming conventions for the SDK project name, the namespaces in your project, and package names. Following these recommendations will help others find the SDK more easily.

### Namespaces

* Main namespace: Kentico Cloud
* Sub namespace: Name of the API (e.g., Delivery)
* Namespace separator: as per target platform
* Letter case: as per target platform

### Project name (GitHub repo name)

We recommend that the project for your SDK uses the following naming pattern: `[Main namespace] <Sub Namespace> <Platform> [Paradigm] SDK`. For example, for the Delivery .NET SDK, the project name would be _Kentico Cloud Delivery .NET SDK_.

## OS project requirements

For your SDK to be included within the list Kentico Cloud SDKs, the SDK project has to comply with our checklist for [Publishing an OS project](https://github.com/Kentico/Home/wiki/Checklist-for-publishing-a-new-OS-project).
