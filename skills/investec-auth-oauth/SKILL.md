---
name: investec-auth-oauth
version: 0.1.0
description: Investec Open API authentication helper (OAuth2 client_credentials and 3-legged OAuth). Use when a developer asks for access tokens, OAuth, refresh tokens, consent/authorize URL construction, scopes, or is getting 400/401/403 from identity endpoints.
tags: [investec, openapi, swagger, auth, oauth2, oidc, 3-legged]
triggers:
  include:
    - "oauth"
    - "3-legged"
    - "authorization code"
    - "auth code"
    - "authorize url"
    - "consent"
    - "access token"
    - "refresh token"
    - "client_credentials"
    - "grant_type"
    - "x-api-key"
    - "401"
    - "403"
    - "identity/v2/oauth2"
  exclude:
    - "non-investec"
    - "aws cognito"
    - "auth0"
    - "okta"
inputs:
  required:
    - client_id
    - client_secret
    - api_key
outputs:
  produces:
    - endpoint_guidance
    - curl_examples
    - parameter_checklist
    - error_troubleshooting
references:
  - path: ../../swagger/open-api-authorization-3-legged-oauth.json
    type: openapi
---

# Investec Auth (OAuth2 + 3-Legged OAuth)

## Scope
Covers Investec identity endpoints for token and authorize flows as documented in the Authorization Swagger.
Does not cover third-party identity providers.

## Base URL
- `https://openapi.investec.com`

## What you'll need
- `client_id`
- `client_secret`
- `x-api-key`
- redirect URI (3-legged only)
- required scopes (product-specific)

## Workflow A - Client Credentials (server-to-server)
Use when there is no end-user consent step.

### Request
- `POST /identity/v2/oauth2/token`
- Headers:
  - `Authorization: Basic base64(client_id:client_secret)`
  - `x-api-key: <api_key>`
  - `Content-Type: application/x-www-form-urlencoded`
- Body:
  - `grant_type=client_credentials`

### curl example
```bash
curl -X POST "https://openapi.investec.com/identity/v2/oauth2/token" \
  -H "Authorization: Basic <base64(client_id:client_secret)>" \
  -H "x-api-key: <api_key>" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials"
```

## Workflow B - 3-Legged OAuth (authorization_code)
Use when a user must consent and the app acts on that user's behalf.

### Step 1: Build authorize URL
- `GET /identity/v2/oauth2/authorize`
- Query params:
  - `scope=<space separated scopes>`
  - `client_id=<client_id>`
  - `redirect_uri=<redirect_uri>`
  - `response_type=code`

### Step 2: Exchange code for tokens
- `POST /identity/v2/oauth2/token`
- Headers:
  - `Authorization: Basic base64(client_id:client_secret)`
  - `x-api-key: <api_key>`
  - `Content-Type: application/x-www-form-urlencoded`
- Body:
  - `grant_type=authorization_code`
  - `code=<code_from_redirect>`
  - `redirect_uri=<redirect_uri>`

The `redirect_uri` must match exactly.

### Step 3: Refresh token
- `POST /identity/v2/oauth2/token`
- Headers: same as above
- Body:
  - `grant_type=refresh_token`
  - `refresh_token=<refresh_token>`

## Common failure modes
- `400 Bad Request`: wrong `grant_type`, missing form fields, or redirect URI mismatch
- `401 Unauthorized`: invalid Basic auth credentials or missing/invalid `x-api-key`
- `403 Forbidden`: token issued successfully, but scopes or permissions do not allow the downstream call

## Don't do this
- Do not paste real secrets or tokens into public issues.
- Do not log bearer tokens or environment variables in client apps.