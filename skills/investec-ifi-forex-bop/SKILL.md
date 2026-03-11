---
name: investec-ifi-forex-bop
version: 0.1.0
description: IFI Forex BOP reporting helper. Covers BOP report listing, get-by-transactionReference, validation endpoints, required documents, document upload/retrieval, workflow actions, and allowances balance.
tags: [investec, openapi, swagger, ifi, forex, bop, balance-of-payments]
triggers:
  include:
    - "forex"
    - "bop"
    - "balance of payments"
    - "/za/ifi/v1/forex"
    - "transactionReference"
    - "required-documents"
    - "bop-reports"
    - "allowances"
    - "documentHandle"
    - "validate"
  exclude:
    - "/za/pb/v1"
    - "/za/bb/v1"
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
  - path: ../../swagger/sa-cib-investec-for-intermediaries-foreign-exchange.json
    type: openapi
---

# SA CIB IFI Foreign Exchange - BOP Reports

## Scope
This skill covers BOP reporting flows and related document/allowance endpoints under `/za/ifi/v1/forex`.

## Auth & headers
Swagger defines bearer auth.
Typical headers:
- `Authorization: Bearer <access_token>`
- `Accept: application/json`
- `Content-Type: application/json` for POST/PUT/PATCH

## Base URL
- `https://openapi.investec.com`

## Common workflows

### 1) List BOP reports
- `GET /za/ifi/v1/forex/bop-reports`

### 2) Get a BOP report for a transaction
- `GET /za/ifi/v1/forex/bop-reports/{transactionReference}`

### 3) Validate a BOP report payload (recommended before update)
Two validation routes exist:
- `POST /za/ifi/v1/forex/bop-reports/validation`
- `POST /za/ifi/v1/forex/bop-reports/{transactionReference}/validation`

Use validation to catch schema/rule issues before PUT.

### 4) Update a BOP report
- `PUT /za/ifi/v1/forex/bop-reports/{transactionReference}`

### 5) Required documents
- `POST /za/ifi/v1/forex/bop-reports/required-documents`

### 6) Documents for a transaction
- List:
  - `GET /za/ifi/v1/forex/bop-reports/{transactionReference}/documents`
- Upload / replace and fetch a specific document:
  - `PUT /za/ifi/v1/forex/bop-reports/{transactionReference}/documents/{documentHandle}`
  - `GET /za/ifi/v1/forex/bop-reports/{transactionReference}/documents/{documentHandle}`

### 7) Workflow actions (state transitions)
- `PATCH /za/ifi/v1/forex/bop-reports/{transactionReference}:{action}`

(Use when the API expects a state change via `{action}`.)

### 8) Allowances balance
- `GET /za/ifi/v1/forex/allowances/{accountNo}/balance`

## Minimal curl example (validate)
```bash
curl -X POST "https://openapi.investec.com/za/ifi/v1/forex/bop-reports/validation" \
  -H "Authorization: Bearer <access_token>" \
  -H "Content-Type: application/json" \
  -d '{ "some": "payload" }'
```

## Common errors
- `400`: validation errors; use a validation endpoint before `PUT`
- `401`: missing or expired token
- `403`: permission mismatch
- `404`: wrong `transactionReference` or `documentHandle`