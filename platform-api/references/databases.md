# Configuration, Create, Create Token, Delete ...

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Authorizations.

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

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

GET/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

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
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate with a Bearer header formatted as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account.

databaseNamestringrequired

The name of the database.

#### Response

200 - application/json

Successful response

size_limitstring

Database size cap in bytes. Unit-suffixed values such as 1mb, 256mb, 1gb are accepted too.

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

Allowed IP addresses and CIDR blocks; an empty list leaves IP access unrestricted.

Example:

```
["203.0.113.7", "10.0.0.0/8"]
```

allowed_aws_vpc_idsstring[]

Allowed AWS VPC endpoint IDs; an empty list leaves VPC endpoint access unrestricted.

Example:

```
["vpce-0fe6c8807461bba49"]
```

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "new-database",
      "group": "default"
  }'

```

```
{
  "database": {
    "DbId": "<unknown>",
    "Hostname": "<unknown>",
    "Name": "<unknown>"
  }
}
```

POST/v1/organizations/{organizationSlug}/databases

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "new-database",
      "group": "default"
  }'

```

```
{
  "database": {
    "DbId": "<unknown>",
    "Hostname": "<unknown>",
    "Name": "<unknown>"
  }
}
```

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "new-database",
      "group": "default"
  }'

```

#### Authorizations

Authorizationstringheaderrequired

Use Bearer authentication in the form `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

#### Body

application/json

Payload describing the database to be created.

namestringrequired

Name for the new database. It must use only lowercase letters, numbers, and dashes, with a maximum length of 64 characters.

groupstringrequired

Name of the group where the database will be created. That group must already exist.

seedobject

Show child attributes

size_limitstring

Maximum database size in bytes. Values may also include units, for example 1mb, 256mb, 1gb.

remote_encryptionobject

Database encryption settings. Use them when creating a new encrypted database, creating an encrypted database from an upload (`seed.type = "database_upload"`), or forking from an encrypted database; they are required when the source database is encrypted.

Show child attributes

#### Response

Successful response

databaseobject

The newly created database

Show child attributes

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens?expiration=2w&authorization=full-access' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

POST/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens?expiration=2w&authorization=full-access' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens?expiration=2w&authorization=full-access' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

#### Authorizations

Authorizationstringheaderrequired

Provide a Bearer authentication header as `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug identifying the organization or user account.

databaseNamestringrequired

The name of the database.

#### Query Parameters

expirationstringdefault:never

Token lifetime, for example 2w1d30m.

authorizationenum<string>default:full-access

Token authorization level, either full-access or read-only.

Options available: `full-access`, `read-only`

#### Body

application/json

Extra token context, including any required claims.

permissionsobject

The permissions for the token.

Show child attributes

#### Response

Successful response

jwtstring

The generated authorization token (JWT).

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "database": "my-db"
}
```

DELETE/v1/organizations/{organizationSlug}/databases/{databaseName}

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "database": "my-db"
}
```

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send the Bearer authentication header as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug value.

databaseNamestringrequired

The name of the database.

#### Response

Successful response

databasestring

Name of the deleted database.

Example:

`"my-db"`

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "could not find database with name [databaseName]: record not found"
}
```

POST/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "could not find database with name [databaseName]: record not found"
}
```

Applying these changes requires brief downtime.

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate using the Bearer header format `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the relevant organization or user account.

databaseNamestringrequired

The name of the database.

#### Response

Successful response (No Content)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "databases": [
    {
      "Name": "my-db",
      "DbId": "0eb771dd-6906-11ee-8553-eaa7715aeaf2",
      "Hostname": "[databaseName]-[organizationSlug].turso.io",
      "block_reads": false,
      "block_writes": false,
      "regions": [
        "aws-us-east-1"
      ],
      "primaryRegion": "aws-us-east-1",
      "group": "default",
      "delete_protection": false,
      "parent": {
        "id": "<string>",
        "name": "<string>",
        "branched_at": "2025-04-15T13:14:34.468213117Z"
      }
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/databases

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "databases": [
    {
      "Name": "my-db",
      "DbId": "0eb771dd-6906-11ee-8553-eaa7715aeaf2",
      "Hostname": "[databaseName]-[organizationSlug].turso.io",
      "block_reads": false,
      "block_writes": false,
      "regions": [
        "aws-us-east-1"
      ],
      "primaryRegion": "aws-us-east-1",
      "group": "default",
      "delete_protection": false,
      "parent": {
        "id": "<string>",
        "name": "<string>",
        "branched_at": "2025-04-15T13:14:34.468213117Z"
      }
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use a Bearer authentication header in the form `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug of the target organization or user account.

#### Query Parameters

groupstring

Filter databases by group name.

schemastring

Schema database name used to retrieve databases under that parent schema.

parentstring

Limit database branch results by parent database ID.

#### Response

200 - application/json

Successful response

databasesobject[]

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "instances": [
    {
      "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
      "name": "aws-us-east-1",
      "type": "primary",
      "region": "aws-us-east-1",
      "hostname": "[databaseName]-[organizationSlug].turso.io"
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/databases/{databaseName}/instances

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "instances": [
    {
      "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
      "name": "aws-us-east-1",
      "type": "primary",
      "region": "aws-us-east-1",
      "hostname": "[databaseName]-[organizationSlug].turso.io"
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Pass Bearer authentication as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account being addressed.

databaseNamestringrequired

The name of the database.

#### Response

200 - application/json

Successful response

instancesobject[]

Show child attributes

## Authorizations

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "database": {
    "Name": "my-db",
    "DbId": "0eb771dd-6906-11ee-8553-eaa7715aeaf2",
    "Hostname": "[databaseName]-[organizationSlug].turso.io",
    "block_reads": false,
    "block_writes": false,
    "regions": [
      "aws-us-east-1"
    ],
    "primaryRegion": "aws-us-east-1",
    "group": "default",
    "delete_protection": false,
    "parent": {
      "id": "<string>",
      "name": "<string>",
      "branched_at": "2025-04-15T13:14:34.468213117Z"
    }
  }
}
```

GET/v1/organizations/{organizationSlug}/databases/{databaseName}

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "database": {
    "Name": "my-db",
    "DbId": "0eb771dd-6906-11ee-8553-eaa7715aeaf2",
    "Hostname": "[databaseName]-[organizationSlug].turso.io",
    "block_reads": false,
    "block_writes": false,
    "regions": [
      "aws-us-east-1"
    ],
    "primaryRegion": "aws-us-east-1",
    "group": "default",
    "delete_protection": false,
    "parent": {
      "id": "<string>",
      "name": "<string>",
      "branched_at": "2025-04-15T13:14:34.468213117Z"
    }
  }
}
```

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Include Bearer authentication with `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug of the organization or user account.

databaseNamestringrequired

The name of the database.

#### Response

Successful response

databaseobject

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "instance": {
    "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
    "name": "aws-us-east-1",
    "type": "primary",
    "region": "aws-us-east-1",
    "hostname": "[databaseName]-[organizationSlug].turso.io"
  }
}
```

GET/v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName}

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "instance": {
    "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
    "name": "aws-us-east-1",
    "type": "primary",
    "region": "aws-us-east-1",
    "hostname": "[databaseName]-[organizationSlug].turso.io"
  }
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName} \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Set the Bearer authentication header to `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account.

databaseNamestringrequired

The name of the database.

instanceNamestringrequired

Instance name, meaning the location code.

#### Response

200 - application/json

Successful response

instanceobject

Show child attributes

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/stats' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "top_queries": [
    {
      "query": "SELECT COUNT(*), CustomerID FROM Orders GROUP BY CustomerID HAVING COUNT(*) > 5;",
      "rows_read": 123,
      "rows_written": 4567
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/databases/{databaseName}/stats

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/stats' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "top_queries": [
    {
      "query": "SELECT COUNT(*), CustomerID FROM Orders GROUP BY CustomerID HAVING COUNT(*) > 5;",
      "rows_read": 123,
      "rows_written": 4567
    }
  ]
}
```

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/stats' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate through a Bearer header formatted as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

databaseNamestringrequired

The name of the database.

#### Response

Successful response

top_queriesobject[] | null

Top queries run against the specified database, plus totals for rows read and rows written.

Show child attributes
