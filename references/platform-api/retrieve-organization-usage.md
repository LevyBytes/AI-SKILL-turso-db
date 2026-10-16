# Retrieve organization usage

[Context index](organization-configuration-and-usage-api.md)

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

GET /v1/organizations/{organizationSlug}/usage

#### Authorizations

**`Authorization`** — string; header; required.

Send the authorization header with Bearer authentication as `Bearer <token>`; use your auth token for `<token>`.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the target organization or user account.

#### Response

200 - application/json

Successful response

**`organization`** — object.

Organization usage object with totals for rows read, rows written, and total storage size (in bytes).

Open when you need the nested `organization` contract: [field details](organization-usage-response-fields.md).
