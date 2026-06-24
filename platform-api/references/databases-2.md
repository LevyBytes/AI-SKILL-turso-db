# Update Configuration, Upload, Usage

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Authorizations; Headers.

## Authorizations

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "size_limit": "500mb",
      "delete_protection": true,
      "block_reads": false,
      "block_writes": false,
      "allowed_ips": ["203.0.113.7", "10.0.0.0/8"],
      "allowed_aws_vpc_ids": ["vpce-0fe6c8807461bba49"]
  }'

```

```
{
  "size_limit": "10000",
  "allow_attach": true,
  "block_reads": false,
  "block_writes": false,
  "delete_protection": true,
  "allowed_ips": [
    "203.0.113.7",
    "10.0.0.0/8"
  ],
  "allowed_aws_vpc_ids": [
    "vpce-0fe6c8807461bba49"
  ]
}
```

PATCH/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "size_limit": "500mb",
      "delete_protection": true,
      "block_reads": false,
      "block_writes": false,
      "allowed_ips": ["203.0.113.7", "10.0.0.0/8"],
      "allowed_aws_vpc_ids": ["vpce-0fe6c8807461bba49"]
  }'

```

```
{
  "size_limit": "10000",
  "allow_attach": true,
  "block_reads": false,
  "block_writes": false,
  "delete_protection": true,
  "allowed_ips": [
    "203.0.113.7",
    "10.0.0.0/8"
  ],
  "allowed_aws_vpc_ids": [
    "vpce-0fe6c8807461bba49"
  ]
}
```

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "size_limit": "500mb",
      "delete_protection": true,
      "block_reads": false,
      "block_writes": false,
      "allowed_ips": ["203.0.113.7", "10.0.0.0/8"],
      "allowed_aws_vpc_ids": ["vpce-0fe6c8807461bba49"]
  }'

```

#### Authorizations

Authorizationstringheaderrequired

Use a Bearer auth header formatted as `Bearer <token>`, with `<token>` set to your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

databaseNamestringrequired

The name of the database.

#### Body

application/json

Configuration settings to apply to the selected database.

size_limitstring

Maximum database size, expressed in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb.

allow_attachbooleandeprecated

Controls whether databases may be attached to the current database.

block_readsboolean

Block all database reads.

Example:

`false`

block_writesboolean

Block all database writes.

Example:

`false`

delete_protectionboolean

Stops deletion of the database.

Example:

`true`

allowed_ipsstring[]

Allowed IP addresses and CIDR blocks. Connections are accepted only from these sources. Use an empty array to remove all entries. Leave omitted to keep the current setting.

Example:

```
["203.0.113.7", "10.0.0.0/8"]

```

allowed_aws_vpc_idsstring[]

Allowed AWS VPC endpoint IDs (vpce-...). Connections must come through one of these endpoints. Use an empty array to remove all entries. Leave omitted to keep the current setting.

Example:

```
["vpce-0fe6c8807461bba49"]

```

#### Response

200 - application/json

Successful response

size_limitstring

Maximum database size, expressed in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb.

Example:

`"10000"`

allow_attachbooleandeprecated

Controls whether databases may be attached to the current database.

Example:

`true`

block_readsboolean

Current blocked-read status.

Example:

`false`

block_writesboolean

Current blocked-write status.

Example:

`false`

delete_protectionboolean

Stops deletion of the database.

Example:

`true`

allowed_ipsstring[]

Allowed IP addresses and CIDR blocks. An empty value indicates there is no IP restriction.

Example:

```
["203.0.113.7", "10.0.0.0/8"]

```

allowed_aws_vpc_idsstring[]

Allowed AWS VPC endpoint IDs. An empty value indicates there is no VPC endpoint restriction.

Example:

```
["vpce-0fe6c8807461bba49"]

```

## Headers

```
curl -X POST 'https://{database_hostname}/v1/upload' \
  -H 'Authorization: Bearer DATABASE_TOKEN' \
  -H 'Content-Length: FILE_SIZE' \
  --data-binary '@/path/to/database.db'

```

POST/v1/upload

```
curl -X POST 'https://{database_hostname}/v1/upload' \
  -H 'Authorization: Bearer DATABASE_TOKEN' \
  -H 'Content-Length: FILE_SIZE' \
  --data-binary '@/path/to/database.db'

```

Upload a SQLite database file into a database created with `seed.type = "database_upload"`. Call this endpoint on the **database hostname**, for example `my-db-my-org.turso.io`, instead of `api.turso.tech`. Before uploading, [create a database](https://docs.turso.tech/api-reference/databases/create) using `seed.type = "database_upload"`. Authentication for this endpoint uses a **database token**, not a platform API token. After the database exists, you can [create a database token](https://docs.turso.tech/api-reference/databases/create-token).

### Headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Yes | Bearer token for the database |
| `Content-Length` | Yes | Database file size, in bytes |
| `x-turso-encryption-key` | No | Base64-encoded encryption key, using 32 bytes for 256-bit ciphers or 16 bytes for 128-bit ciphers |
| `x-turso-encryption-cipher` | No | Encryption cipher. Supported values are listed in the table below. |

### Request Body

 Raw binary SQLite database file.

### Database File Requirements

 The SQLite database file is required to satisfy the following conditions:
- **Journal mode**: WAL (`PRAGMA journal_mode = WAL`)
- **Page size**: 4096 bytes (`PRAGMA page_size = 4096`)
- **Auto vacuum**: Disabled (`PRAGMA auto_vacuum = 0`)
- **Encoding**: UTF-8 (`PRAGMA encoding = 'UTF-8'`)
 Encrypted databases must also configure reserved bytes for the selected cipher:
| Cipher | Reserved Bytes |
| --- | --- |
| `aes256gcm` | 28 |
| `aes128gcm` | 28 |
| `chacha20poly1305` | 28 |
| `aegis128l` | 32 |
| `aegis128x2` | 32 |
| `aegis128x4` | 32 |
| `aegis256` | 48 |
| `aegis256x2` | 48 |
| `aegis256x4` | 48 |

### Responses

### Success

 On success, returns no body and status `200 OK`.

### Errors

| Status | Description |
| --- | --- |
| `400` | Database file is invalid because of format, settings, or corruption |
| `401` | Invalid or missing authorization token |

```
curl -X POST 'https://{database_hostname}/v1/upload' \
  -H 'Authorization: Bearer DATABASE_TOKEN' \
  -H 'Content-Length: FILE_SIZE' \
  --data-binary '@/path/to/database.db'

```

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/usage?from=2023-01-01T00:00:00Z&to=2023-02-01T00:00:00Z' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "database": {
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
}
```

GET/v1/organizations/{organizationSlug}/databases/{databaseName}/usage

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/usage?from=2023-01-01T00:00:00Z&to=2023-02-01T00:00:00Z' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "database": {
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
}
```

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/usage?from=2023-01-01T00:00:00Z&to=2023-02-01T00:00:00Z' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use a Bearer auth header formatted as `Bearer <token>`, with `<token>` set to your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

databaseNamestringrequired

The name of the database.

#### Query Parameters

fromstring<date-time>

Start datetime for usage retrieval, provided in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. If omitted, the current calendar month is used. Example: `2023-01-01T00:00:00Z`

tostring<date-time>

End datetime for usage retrieval, provided in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. If omitted, the current calendar month is used. Example: `2023-02-01T00:00:00Z`

#### Response

Successful response

databaseobject

Database usage object that includes total usage and per-instance usage for rows read and rows written, plus total storage size in bytes.

Show child attributes
