---
name: Authenticate and read learning data (Cool College)
description: Obtain an access_token via the HMAC-signature exchange, then read an enterprise's learning tasks, exams, and certificates from the Cool College Open API.
api: openapi/coolcollege-openapi-original.json
operations:
  - "GET /{enterprise-id}/get_access_token"
  - "GET /{enterprise-id}/plans"
  - "GET /{enterprise-id}/plans/{plan-id}"
  - "GET /{enterprise-id}/new_exams"
  - "GET /{enterprise-id}/get_certificate_list"
---

# Authenticate and read learning data

Base URL: `https://openapi.coolcollege.cn/api/v2`. Every path is scoped to a tenant via the `{enterprise-id}` path parameter.

## 1. Get an access token
Call `GET /{enterprise-id}/get_access_token` with query params `api_key`, `api_secret`, `timestamp` (13-digit ms), and `signature`.
Compute `signature = HmacSHA256(timestamp + "\n" + apiKey, key=apiSecret)`, then URL-encode it before appending. The response returns an `access_token` used to authorize subsequent calls (see the open platform docs for the exact binding).

## 2. List learning tasks
`GET /{enterprise-id}/plans` with `page_number`/`page_size` (and optional `begin_time`/`end_time`). For a single task's completion status use `GET /{enterprise-id}/plans/{plan-id}`.

## 3. Read exams
Use `GET /{enterprise-id}/new_exams` (the current endpoint — the older `/exams` is marked 即将废弃 / soon-deprecated). Drill into participants with `GET /{enterprise-id}/new_exams/{exam-id}`.

## 4. Read certificates
`GET /{enterprise-id}/get_certificate_list` for the enterprise's certificates; `GET /{enterprise-id}/users/{user-id}/certificate` for one person's detail.

## Conventions
- Pagination is offset-based via `page_number` + `page_size`.
- Responses are wrapped in the `ApiResponse` envelope (`code`/`type`/`message`) — check `code` before trusting the body.
