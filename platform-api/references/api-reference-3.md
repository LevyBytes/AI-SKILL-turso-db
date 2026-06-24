# Quickstart, Response Codes, Tokens, User

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: TypeScript; Response Codes; Authorizations.

## TypeScript

**Using TypeScript?** Rather than working through raw HTTP samples, you can use the [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client. For setup, see the [SDK quickstart](https://docs.turso.tech/api-reference/introduction#quickstart-with-the-typescript-sdk). 1

Sign up or log in with the Turso CLI

If you have not done so yet, [install the Turso CLI](https://docs.turso.tech/cli/installation).

```
turso auth signup

```

2

Find your account or organization slug

The Platform API works with either a personal account or an organization. Use the Turso CLI to find the `slug` for that account or organization:

```
turso org list

```

3

Generate a new Platform API Token

Next, use the Turso CLI to create a new API Token:

```
turso auth api-tokens mint quickstart

```

To limit the token to one organization, add `--org`:

```
turso auth api-tokens mint quickstart --org my-org

```

Store the token in a secure place; it is needed in the next step.4

Fetch available Locations

To create a group or database in a chosen region, first request the available region list:

```
curl -L 'https://api.turso.tech/v1/locations' \
  -H 'Authorization: Bearer TOKEN' \

```

`organizationSlug` identifies your organization or personal account.5

Create a Group

Every database is placed in a group, and groups may span one or more locations. Start by creating a group with a `name` and primary `location`:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "default",
      "location": "lhr"
  }'

```

6

Create a Database

Create the first database inside the group from the previous step:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "my-db",
      "group": "default"
  }'

```

7

Connect to your database

At this point, the database exists and is distributed across multiple regions; connect with one of the official or experimental SDKs:

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### SQL over HTTP

## Response Codes

The Turso API returns an HTTP status code. If the request fails, the response body includes an error message.
| Code | Description |
| --- | --- |
| `200` | `OK` — Successful request |
| `401` | `Unauthorized` — Invalid or expired auth token |
| `402` | `Payment required` - Confirm that an active subscription is in place |
| `403` | `Forbidden` - Your credentials are not permitted to access this resource |
| `409` | `Conflict` — Resource already exists |

## Authorizations

```
curl -L -X POST https://api.turso.tech/v1/auth/api-tokens/{tokenName} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"organization": "my-org"}'

```

```
{
  "name": "<unknown>",
  "id": "<unknown>",
  "token": "..."
}
```

POST/v1/auth/api-tokens/{tokenName}

```
curl -L -X POST https://api.turso.tech/v1/auth/api-tokens/{tokenName} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"organization": "my-org"}'

```

```
{
  "name": "<unknown>",
  "id": "<unknown>",
  "token": "..."
}
```

The response shows `token` only once. Save it securely, and do not share it or commit it to source control. **A future release will remove unrestricted (cross-org) tokens, which are deprecated.** Include at least `organization` in every request body.

```
curl -L -X POST https://api.turso.tech/v1/auth/api-tokens/{tokenName} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"organization": "my-org"}'

```

#### Authorizations

Authorizationstringheaderrequired

Use a Bearer authentication header in the format `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

tokenNamestringrequired

Name assigned to the api token.

#### Body

application/json

Optional token restriction. Leave the body out to create an unrestricted token; provide only `organization` for org scope, or provide `organization` + `group` + `scopes` when the token should be group-scoped.

organizationstring

Organization slug used to constrain this token. It is required if `group` is provided.

Example:

`"my-org"`

groupstring

Group name within `organization` that this token is limited to. This requires `organization` plus a non-empty `scopes` list.

Example:

`"default"`

scopesenum<string>[]

Permissions assigned to a group-scoped token. Entries may be single scopes or the presets `read-only` (which maps to `read`) and `full-access` (which maps to all scopes). When `group` is supplied, this list is required and cannot be empty. `db:mint-token` can mint new SQL credentials, while `db:rotate-creds` invalidates all existing SQL tokens for the database; rotation is destructive, so these permissions are kept separate.

Supported options: `read`, `db:create`, `db:delete`, `db:configure`, `db:mint-token`, `db:rotate-creds`, `group:configure`, `group:mint-token`, `group:rotate-creds`, `read-only`, `full-access` Example:

```
[
  "db:create",
  "db:configure",
  "db:mint-token"
]
```

#### Response

200 - application/json

Successful response

nameanyidanytokenstring

The token value itself, returned as a JWT. Use it in the `Bearer` header; for background, see [Authentication](https://docs.turso.tech/authentication). It will not be shown again.

Example:

`"..."`

## Authorizations

```
curl -L https://api.turso.tech/v1/auth/api-tokens \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "my-token",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "created_at": "2026-05-21"
    }
  ]
}
```

GET/v1/auth/api-tokens

```
curl -L https://api.turso.tech/v1/auth/api-tokens \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "my-token",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "created_at": "2026-05-21"
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/auth/api-tokens \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate with a Bearer header formatted as `Bearer <token>`, replacing `<token>` with your auth token.

#### Response

200 - application/json

Successful response

tokensobject[]

Show child attributes

## Authorizations

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "ci-bot",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "owner": {
        "username": "alice",
        "email": "alice@example.com"
      },
      "created_at": "2026-05-21"
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/api-tokens

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "ci-bot",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "owner": {
        "username": "alice",
        "email": "alice@example.com"
      },
      "created_at": "2026-05-21"
    }
  ]
}
```

This endpoint lists tokens that are valid for one organization, including org-scoped and group-scoped tokens. Admins and owners can view every organization token and the user who minted it; members and viewers receive only their own tokens. To enumerate tokens for all organizations the caller belongs to, call [`GET /v1/auth/api-tokens`](https://docs.turso.tech/api-reference/tokens/list) instead.

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send a Bearer authentication header as `Bearer <token>`; `<token>` must be your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account.

#### Response

200 - application/json

Successful response

tokensobject[]

Show child attributes

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/auth/api-tokens/{tokenName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "..."
}
```

DELETE/v1/auth/api-tokens/{tokenName}

```
curl -L -X DELETE 'https://api.turso.tech/v1/auth/api-tokens/{tokenName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "..."
}
```

```
curl -L -X DELETE 'https://api.turso.tech/v1/auth/api-tokens/{tokenName}' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Provide Bearer authentication in the header, using `Bearer <token>` with `<token>` set to your auth token.

#### Path Parameters

tokenNamestringrequired

Name assigned to the api token.

#### Response

200 - application/json

Successful response

tokenstring

The revoked token name.

Example:

`"..."`

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens/{tokenId}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "clGFZ4STEe6fljpFzIum8A"
}
```

DELETE/v1/organizations/{organizationSlug}/api-tokens/{tokenId}

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens/{tokenId}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "clGFZ4STEe6fljpFzIum8A"
}
```

This endpoint requires the token **ID** instead of its name, because multiple users in an organization can create tokens with the same name. Admins and owners can revoke any token scoped to that organization; members and viewers can revoke only tokens they minted. For unrestricted tokens, use [`DELETE /v1/auth/api-tokens/{tokenName}`](https://docs.turso.tech/api-reference/tokens/revoke).

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens/{tokenId}' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use the authorization header `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account.

tokenIdstringrequired

Token ID to revoke, taken from the list endpoint.

#### Response

200 - application/json

Successful response

tokenstring

ID of the token that was revoked.

Example:

`"clGFZ4STEe6fljpFzIum8A"`

## Authorizations

```
curl -L 'https://api.turso.tech/v1/auth/validate' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "exp": 999
}
```

GET/v1/auth/validate

```
curl -L 'https://api.turso.tech/v1/auth/validate' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "exp": 999
}
```

```
curl -L 'https://api.turso.tech/v1/auth/validate' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate by sending the header value `Bearer <token>`; `<token>` is your auth token.

#### Response

200 - application/json

Successful response

expinteger

Expiration time for the supplied token, represented as unix epoch seconds; `-1` means it does not expire.

Example:

`999`

## Authorizations

```
curl -L https://api.turso.tech/v1/user \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "user": {
    "username": "iku",
    "name": "Iku Turso",
    "email": "iku@turso.tech",
    "avatarUrl": "https://avatars.githubusercontent.com/u/12345",
    "plan": "scaler",
    "mfa": false,
    "has_pending_invites": false
  }
}
```

GET/v1/user

```
curl -L https://api.turso.tech/v1/user \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "user": {
    "username": "iku",
    "name": "Iku Turso",
    "email": "iku@turso.tech",
    "avatarUrl": "https://avatars.githubusercontent.com/u/12345",
    "plan": "scaler",
    "mfa": false,
    "has_pending_invites": false
  }
}
```

```
curl -L https://api.turso.tech/v1/user \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send a Bearer auth header in the form `Bearer <token>`, with `<token>` replaced by your auth token.

#### Response

200 - application/json

Successful response

userobject

Show child attributes
