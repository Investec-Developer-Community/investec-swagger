---
name: investec-card-code
version: 0.1.0
description: Investec SA Card Code helper. Covers card listing, programmable feature toggling, uploading code, publishing code, execution simulation, execution history, env vars, and reference data endpoints (countries/currencies/merchants).
tags: [investec, openapi, swagger, card, programmable, card-code]
triggers:
  include:
    - "card code"
    - "programmable card"
    - "/za/v1/cards"
    - "publish"
    - "publishedcode"
    - "execute"
    - "executions"
    - "environmentvariables"
    - "toggle-programmable-feature"
    - "merchants"
    - "currencies"
    - "countries"
  exclude:
    - "/za/pb/v1"
    - "/za/bb/v1"
    - "/za/ifi/"
    - "beneficiary"
    - "eft"
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
  - path: ../../swagger/sa-card-code.json
    type: openapi
---

# SA Card Code

## Scope
Use this skill for Investec Programmable Card (“Card Code”) endpoints under `/za/v1/cards`.

## Auth & headers
Swagger defines OAuth2 scope `cards`.
Typical headers:
- `Authorization: Bearer <access_token>`
- `Accept: application/json`
- `Content-Type: application/json` for POSTs

## Base URL
- `https://openapi.investec.com`

## Core concepts
- **cardKey**: key identifier used in path params for most operations.
- **Code vs Published code**:
  - “Code” is what you’ve uploaded/edited
  - “Published code” is what’s actively running
- **Programmable feature toggle**: can enable/disable programmable behaviour per card.

## Common workflows

### 1) List cards / create card record
- `GET /za/v1/cards`
- `POST /za/v1/cards`

### 2) Get/update a card
- `GET /za/v1/cards/{cardKey}`
- `POST /za/v1/cards/{cardKey}`

### 3) Upload or update code (draft)
- `GET /za/v1/cards/{cardKey}/code`
- `POST /za/v1/cards/{cardKey}/code`

### 4) Publish code
- `POST /za/v1/cards/{cardKey}/publish`

### 5) Fetch published code
- `GET /za/v1/cards/{cardKey}/publishedcode`

### 6) Execute code (simulation)
- `POST /za/v1/cards/{cardKey}/code/execute`

Use this to test a payload and observe decision logic without waiting for real-world authorisation flows.

### 7) Execution history
- `GET /za/v1/cards/{cardKey}/code/executions`

### 8) Environment variables
- `GET /za/v1/cards/{cardKey}/environmentvariables`
- `POST /za/v1/cards/{cardKey}/environmentvariables`

Treat these as secrets:
- Don’t log them
- Don’t commit them

### 9) Toggle programmable feature
- `POST /za/v1/cards/{cardKey}/toggle-programmable-feature`

### 10) Reference data
- `GET /za/v1/cards/countries`
- `GET /za/v1/cards/currencies`
- `GET /za/v1/cards/merchants`

## Common errors
- **401**: token missing/expired
- **403**: missing `cards` scope
- **404**: wrong `cardKey`
- **400**: invalid payload for code/env var/execute endpoints

## Minimal curl example (execute simulation)
```bash
curl -X POST "https://openapi.investec.com/za/v1/cards/<cardKey>/code/execute" \
  -H "Authorization: Bearer <access_token>" \
  -H "Content-Type: application/json" \
  -d '{ "some": "payload" }'
```