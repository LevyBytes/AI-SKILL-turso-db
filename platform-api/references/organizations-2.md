# Update, Usage

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Authorizations.

## Authorizations

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "overages": true
  }'

```

```
{
  "organization": {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
}
```

PATCH/v1/organizations/{organizationSlug}

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "overages": true
  }'

```

```
{
  "organization": {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
}
```

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "overages": true
  }'

```

#### Authorizations

Authorizationstringheaderrequired

Use Bearer authentication in the header as `Bearer <token>`; `<token>` should be the auth token for the request.

#### Path Parameters

organizationSlugstringrequired

Slug identifying the organization or user account.

#### Body

application/json

The updated organization details.

overagesboolean

Controls whether organization overages are enabled or disabled.

require_mfaboolean

Make multi-factor authentication mandatory for every organization member. Before turning on this requirement, the requesting user must already have MFA enabled for their own account.

#### Response

200 - application/json

Successful response

organizationobject

The updated organization.

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/usage \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "organization": {
    "uuid": "fa72ba58-7db3-436b-bcab-14b83eb1dd88",
    "usage": {
      "rows_read": 1000000000,
      "rows_written": 25000000,
      "databases": 500,
      "locations": 3,
      "storage_bytes": 9000000000,
      "groups": 1,
      "bytes_synced": 3000000000
    },
    "databases": [
      {
        "uuid": "<unknown>",
        "instances": [
          {
            "uuid": "cd831986-94e5-11ee-a6fe-7a52e1f7759a",
            "usage": {
              "rows_read": 0,
              "rows_written": 0,
              "storage_bytes": 4096,
              "bytes_synced": 0
            }
          },
          {
            "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
            "usage": {
              "rows_read": 0,
              "rows_written": 0,
              "storage_bytes": 4096,
              "bytes_synced": 0
            }
          }
        ],
        "total": {
          "rows_read": 0,
          "rows_written": 0,
          "storage_bytes": 8192,
          "bytes_synced": 0
        }
      }
    ]
  }
}
```

GET/v1/organizations/{organizationSlug}/usage

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/usage \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "organization": {
    "uuid": "fa72ba58-7db3-436b-bcab-14b83eb1dd88",
    "usage": {
      "rows_read": 1000000000,
      "rows_written": 25000000,
      "databases": 500,
      "locations": 3,
      "storage_bytes": 9000000000,
      "groups": 1,
      "bytes_synced": 3000000000
    },
    "databases": [
      {
        "uuid": "<unknown>",
        "instances": [
          {
            "uuid": "cd831986-94e5-11ee-a6fe-7a52e1f7759a",
            "usage": {
              "rows_read": 0,
              "rows_written": 0,
              "storage_bytes": 4096,
              "bytes_synced": 0
            }
          },
          {
            "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
            "usage": {
              "rows_read": 0,
              "rows_written": 0,
              "storage_bytes": 4096,
              "bytes_synced": 0
            }
          }
        ],
        "total": {
          "rows_read": 0,
          "rows_written": 0,
          "storage_bytes": 8192,
          "bytes_synced": 0
        }
      }
    ]
  }
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/usage \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send the authorization header with Bearer authentication as `Bearer <token>`; use your auth token for `<token>`.

#### Path Parameters

organizationSlugstringrequired

Slug for the target organization or user account.

#### Response

200 - application/json

Successful response

organizationobject

Organization usage object with totals for rows read, rows written, and total storage size (in bytes).

Show child attributes
