---
name: investec-cib-account-info
version: 0.1.0
description: Investec SA CIB account information helper. Covers account lists, balances, transactions, companies, and current orders and shipment reporting endpoints.
tags: [investec, openapi, swagger, cib, corporate, accounts, transactions]
triggers:
  include:
    - "cib"
    - "/za/bb/v1"
    - "corporate banking"
    - "companies"
    - "transactions"
    - "shipment"
    - "current orders"
  exclude:
    - "/za/pb/v1"
    - "/za/ifi/"
    - "card code"
inputs:
  required:
    - access_token
outputs:
  produces:
    - endpoint_guidance
    - curl_examples
    - workflow_steps
    - troubleshooting_steps
references:
  - path: ../../swagger/sa-cib-account-information.json
    type: openapi
---

# SA CIB Account Information

## Scope
Use this skill for Corporate and Institutional Banking read-only endpoints under `/za/bb/v1`.

## Auth and headers
The current OpenAPI security scheme is bearer auth.
Typical request headers:
- `Authorization: Bearer <access_token>`
- `Accept: application/json`

Note: some embedded examples in the source spec still show legacy Basic auth examples. Prefer the declared security scheme for new integrations unless your onboarding docs say otherwise.

## Base URL
- `https://openapi.investec.com`

## Common workflows

### 1) List accounts with balances
- `GET /za/bb/v1/accounts`

### 2) Get account transactions
- `GET /za/bb/v1/accounts/{accountId}/transactions?fromDate={fromDate}&toDate={toDate}&page={page}`

Use date filters and pagination to keep responses manageable.

### 3) List companies in the profile
- `GET /za/bb/v1/companies`

### 4) Get current orders and shipment status report
- `GET /za/bb/v1/companies/{companyname}/currentOrdersAndShipmentStatusReport`

### 5) Get current order and shipment line items
- `GET /za/bb/v1/companies/{companyname}/currentOrdersAndShipmentStatusLineItems`

## Common errors
- `401`: missing or expired token
- `403`: token is valid but not permitted for the requested CIB route
- `404`: wrong `accountId` or `companyname`
- `400`: invalid query parameters such as malformed dates or page values

## Minimal curl example (list accounts)
```bash
curl -X GET "https://openapi.investec.com/za/bb/v1/accounts" \
  -H "Authorization: Bearer <access_token>" \
  -H "Accept: application/json"
```
