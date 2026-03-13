---
title: Dispatcher ETag Enhancement for CDN Revalidation
description: Availability, support status, and behavior of INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT in AEM as a Cloud Service.
---
# Dispatcher ETag Enhancement for CDN Revalidation

## Overview

The `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` flag enables Dispatcher to evaluate the `If-None-Match` request header on cache hits. When the incoming `If-None-Match` value matches the cached `ETag`, Dispatcher can return `304 Not Modified` instead of `200 OK`.

This behavior is designed to reduce unnecessary payload transfer between CDN and Dispatcher and increase conditional-cache efficiency.

## Availability

- Backing change: `SKYOPS-120269`
- Dispatcher version: `2.0.264`
- AEM SDK build: `aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Service support

In AEM as a Cloud Service, this capability is supported for customer use.

Customers can enable it by setting the `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` environment variable in Cloud Manager. Adobe can also enable it on behalf of the customer when needed.

When enabled, and when the CDN sends `If-None-Match` and the relevant `ETag` is present in Dispatcher cache, higher `304` response rates between CDN and Dispatcher are expected. This increase is the intended outcome.

## Configuration example (cache ETag header)

For this enhancement to be effective, make sure Dispatcher caches the `ETag` response header and your web server is configured to avoid generating file-system-based ETags.

Example `dispatcher.any` cache section:

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Example Apache directive in the Dispatcher vhost context:

```apache
FileETag none
```

For baseline header-caching guidance, see [Caching HTTP response headers](dispatcher-configuration.md#caching-http-response-headers).

## Validation example

After enabling the environment variable and deploying config changes:

1. Request once to warm the cache and capture the returned `ETag`.
1. Request again with `If-None-Match: <etag-value>`.
1. Confirm Dispatcher returns `304 Not Modified` for cache-hit revalidation flows.

## Current documentation status

There is no public product documentation for this feature at this time. Treat this as a supported feature that is not yet publicly documented.

Use this page as an interim internal reference until public documentation is published.

## Public reference (related behavior)

For customer-facing baseline guidance on header caching and `ETag` handling in Dispatcher, reference:

- [Configure Dispatcher - Caching HTTP response headers](https://experienceleague.adobe.com/en/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

This public page covers caching the `ETag` header and using `FileETag none`. It does not currently document the `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` feature flag itself.

## Suggested customer-facing wording

"This capability is available in Dispatcher `2.0.264` (AEM SDK `2026.2.24464`). When enabled, Dispatcher can validate `If-None-Match` against cached `ETag` values and return `304 Not Modified` on cache hits. In AEM as a Cloud Service, this is supported and can be enabled through Cloud Manager environment configuration."
