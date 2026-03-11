---
name: investec-ifi-intermediaries-core
version: 0.1.0
description: Investec for Intermediaries (IFI) core helper. Covers intermediary context, accounts and transactions endpoints, categories CRUD, introducers, banks reference data, client dashboards, and related search/summary routes.
tags: [investec, openapi, swagger, ifi, intermediaries, adviser, wealth]
triggers:
  include:
    - "ifi"
    - "/za/ifi/v1"
    - "intermediary"
    - "intermediaryId"
    - "client dashboard"
    - "categories"
    - "introducers"
    - "banks"
    - "pending-transactions"
    - "accounts summary"
    - "book value"
  exclude:
    - "/za/pb/v1"
    - "/za/bb/v1"
    - "card code"
inputs:
  required:
    - access_token
    - intermediaryId
outputs:
  produces:
    - endpoint_guidance
    - curl_examples
    - header_requirements
    - workflow_steps
    - troubleshooting_steps
references:
  - path: ../../swagger/sa-cib-investec-for-intermediaries.json
    type: openapi
---

# SA CIB Investec for Intermediaries (IFI) - Core

## Scope
This skill covers the IFI core API in `/za/ifi/v1`.

## Auth & required headers
Most endpoints require:
- `Authorization: Bearer <access_token>`
- `intermediaryId: <intermediaryId>` (required header; this is explicit in operations)

## Base URL
- `https://openapi.investec.com`

## Key rule: intermediary context
Most operations require the `intermediaryId` header. If that header is missing, requests are likely to fail.

## Common workflows (endpoint map)

### Accounts & transactions
- Pending transactions:
  - `GET /za/ifi/v1/accounts/{accountNumber}/pending-transactions`
- Transactions:
  - `GET /za/ifi/v1/accounts/{accountNumber}/transactions`
- Balance (bulk-style request):
  - `POST /za/ifi/v1/accounts/balance`
- Accounts for intermediary:
  - `GET /za/ifi/v1/accounts/intermediary`
- Intermediary clients by status:
  - `GET /za/ifi/v1/accounts/intermediary/clients/{accountStatus}`
- Book value:
  - `GET /za/ifi/v1/accounts/book-value`
- Account details:
  - `GET /za/ifi/v1/accounts/{accountNumber}/account-details`
- Summary search:
  - `GET /za/ifi/v1/accounts/summary/{accountStatus}/search/{keyword}`
- Accounts by type:
  - `GET /za/ifi/v1/accounts/{accountType}`

### Reference data
- Banks:
  - `GET /za/ifi/v1/banks`

### Categories (CRUD-ish)
- List/create/update (patch):
  - `GET /za/ifi/v1/intermediaries/categories`
  - `POST /za/ifi/v1/intermediaries/categories`
  - `PATCH /za/ifi/v1/intermediaries/categories`
- Delete:
  - `DELETE /za/ifi/v1/intermediaries/categories/{categoryId}`
- Set default:
  - `POST /za/ifi/v1/intermediaries/categories/default/{categoryId}`

### Client dashboard and details
- Client detail:
  - `GET /za/ifi/v1/clients/accounts/{accountNumber}/client-detail`
- Client dashboard:
  - `GET /za/ifi/v1/clients/client-dashboard/{searchKeyword}`

## Minimal curl example (pending transactions)
```bash
curl -X GET "https://openapi.investec.com/za/ifi/v1/accounts/<accountNumber>/pending-transactions" \
  -H "Authorization: Bearer <access_token>" \
  -H "intermediaryId: <intermediaryId>" \
  -H "Accept: application/json"
```

## Common errors
- `400`: missing or invalid `intermediaryId` header, or invalid path params
- `401`: missing or expired token
- `403`: permissions or scope mismatch
- `404`: wrong account number or route