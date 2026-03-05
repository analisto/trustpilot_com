# Trustpilot Categories API (Observed Next.js Data Endpoint)

## Overview
This document describes the API pattern observed in network captures from:

- `https://www.trustpilot.com/categories`
- `https://www.trustpilot.com/categories/<category-slug>`

The endpoint is a Next.js data route used by Trustpilot's frontend.

## Base Endpoint Pattern

```text
GET https://www.trustpilot.com/_next/data/<build-id>/categories/<slug>.json
```

Example observed build id:

- `categoriespages-consumersite-2.1186.0`

Example full URL:

```text
https://www.trustpilot.com/_next/data/categoriespages-consumersite-2.1186.0/categories/craftsman.json?categoryId=craftsman&page=2
```

## Query Parameters

| Name | Required | Type | Description | Example |
|---|---|---|---|---|
| `categoryId` | Yes | string | Category identifier used by backend filtering. In samples it matches slug. | `craftsman` |
| `page` | No | integer | Pagination page index for category listing results. Omit for first page. | `2` |

### Notes

- `categoryId` is required in all observed requests.
- `page` is optional:
  - first page request observed without `page`
  - subsequent pages use `page=2`, `page=3`, etc.

## Path Parameters

| Name | Required | Description | Example |
|---|---|---|---|
| `<build-id>` | Yes | Next.js build version segment. Can change when Trustpilot deploys a new build. | `categoriespages-consumersite-2.1186.0` |
| `<slug>` | Yes | Category route slug in URL path. | `craftsman` |

### Slug and categoryId relationship

In captured requests:

- path slug: `craftsman`
- query `categoryId`: `craftsman`

They matched exactly.

## Headers

### Minimal practical headers

The observed endpoint was successfully called by browser requests with many headers. For scripted access, these are the practical minimum to start with:

- `x-nextjs-data: 1`
- `user-agent: <browser-like string>`

### Common browser headers seen (usually optional for scripts)

- `accept: */*`
- `accept-language: ...`
- `accept-encoding: gzip, deflate, br, zstd`
- `referer: https://www.trustpilot.com/categories` (or category page)
- many session-specific observability/security headers (`sentry-trace`, `traceparent`, `newrelic`, etc.)
- cookies

## Pagination Behavior (Observed)

### First page

```http
GET /_next/data/<build-id>/categories/craftsman.json?categoryId=craftsman
```

### Page 2

```http
GET /_next/data/<build-id>/categories/craftsman.json?categoryId=craftsman&page=2
```

## cURL Examples

### First page

```bash
curl "https://www.trustpilot.com/_next/data/categoriespages-consumersite-2.1186.0/categories/craftsman.json?categoryId=craftsman" \
  -H "x-nextjs-data: 1" \
  -H "user-agent: Mozilla/5.0"
```

### Paginated page

```bash
curl "https://www.trustpilot.com/_next/data/categoriespages-consumersite-2.1186.0/categories/craftsman.json?categoryId=craftsman&page=2" \
  -H "x-nextjs-data: 1" \
  -H "user-agent: Mozilla/5.0"
```

## Reliability Considerations

- `<build-id>` is deployment-dependent and may expire/change.
- Heavy cookie/header sets captured from browser devtools are not stable; avoid hard-coding them unless required.
- Anti-bot controls may appear over time, requiring request tuning or session handling.

## Source

Document derived from:

- `examples/api_params.txt`
