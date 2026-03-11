---
name: investec-pb-account-info
version: 0.1.0
description: Investec SA Private Banking Account Information helper. Covers accounts, balances, transactions (incl pending), beneficiaries, payments (paymultiple), transfers (transfermultiple), profiles, and documents endpoints.
tags: [investec, openapi, swagger, pb, private-banking, accounts, transactions, payments]
triggers:
  include:
    - "pb"
    - "private banking"
    - "/za/pb/v1"
    - "list accounts"
    - "balance"
    - "transactions"
    - "pending-transactions"
    - "beneficiaries"
    - "paymultiple"
    - "transfermultiple"
    - "profiles"
    - "authorisationsetupdetails"
    - "documents"
    - "statement"
  exclude:
    - "/za/bb/v1"
    - "cib"
    - "/za/ifi/"
    - "intermediaryId"
    - "card code"
inputs:
  required:
    - access_token
outputs:
  produces:
    - endpoint_guidance
    - curl_examples
    - workflow_steps
    - error_troubleshooting
references:
  - path: ../../swagger/sa-pb-account-information.json
    type: openapi
---

# SA PB Account Information

## Scope
Use this skill for **Private Banking** account data and related workflows in `/za/pb/v1`.

## Auth & headers
Swagger defines OAuth2 schemes for PB.
Typical request headers:
- `Authorization: Bearer <access_token>`
- `Accept: application/json`
- `Content-Type: application/json` (POSTs)

(If the calling context also requires `x-api-key`, follow your app’s auth setup.)

## Base URL
- `https://openapi.investec.com`

## Quick-start workflows

### 1) List accounts
- `GET /za/pb/v1/accounts`

### 2) Get balance
- `GET /za/pb/v1/accounts/{accountId}/balance`

### 3) Get transactions
- `GET /za/pb/v1/accounts/{accountId}/transactions`

Tip: use date filters if available in your client pattern (even if not required) to keep responses small.

### 4) Get pending transactions
- `GET /za/pb/v1/accounts/{accountId}/pending-transactions`

### 5) Profiles and profile accounts
- `GET /za/pb/v1/profiles`
- `GET /za/pb/v1/profiles/{profileid}/accounts`

### 6) Authorisation setup details (important for payments/approvals)
- `GET /za/pb/v1/profiles/{profileid}/accounts/{accountid}/authorisationsetupdetails`

### 7) Beneficiaries (global and profile-scoped)
- `GET /za/pb/v1/accounts/beneficiaries`
- `GET /za/pb/v1/accounts/beneficiarycategories`
- `GET /za/pb/v1/profiles/{profileid}/accounts/{accountid}/beneficiaries`

### 8) Payments (batch)
- `POST /za/pb/v1/accounts/{accountId}/paymultiple`

Notes:
- Use the authorisation setup details endpoint first if the payload requires authoriser/approver fields.
- Treat payment calls as “high stakes”: validate payloads carefully and handle partial failures.

### 9) Transfers (batch)
Swagger exposes both:
- `POST /za/pb/v1/accounts/transfermultiple`
- `POST /za/pb/v1/accounts/{accountId}/transfermultiple`

Choose based on whether you’re initiating transfers from a specific account context.

### 10) Documents / statements
- `GET /za/pb/v1/accounts/{accountId}/documents`
- `GET /za/pb/v1/accounts/{accountId}/document/{documentType}/{documentDate}`

## Common errors
- **401**: missing/expired token
- **403**: token scopes don’t permit PB endpoints
- **404**: wrong `accountId` / using PB endpoint with non-PB account
- **400**: validation errors in payment/transfer payloads

## Minimal curl example (list accounts)
```bash
curl -X GET "https://openapi.investec.com/za/pb/v1/accounts" \
  -H "Authorization: Bearer <access_token>" \
  -H "Accept: application/json"
```