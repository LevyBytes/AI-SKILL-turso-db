# Update database configuration request fields

[Context index](database-configuration-upload-and-usage.md) · [Setup and preceding context](update-database-configuration-example.md) · [Continue](update-database-configuration-response-fields.md)

#### Authorizations

**`Authorization`** — string; header; required.

Use a Bearer auth header formatted as `Bearer <token>`, with `<token>` set to your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Organization or user account slug.

**`databaseName`** — string; required.

The name of the database.

#### Body

application/json

Configuration settings to apply to the selected database.

**`size_limit`** — string.

Maximum database size, expressed in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb.

**`allow_attach`** — boolean; deprecated.

Controls whether databases may be attached to the current database.

**`block_reads`** — boolean.

Block all database reads.

Example:

`false`

**`block_writes`** — boolean.

Block all database writes.

Example:

`false`

**`delete_protection`** — boolean.

Stops deletion of the database.

Example:

`true`

**`allowed_ips`** — string[].

Allowed IP addresses and CIDR blocks. Connections are accepted only from these sources. Use an empty array to remove all entries. Leave omitted to keep the current setting.

Example:

```
["203.0.113.7", "10.0.0.0/8"]

```

**`allowed_aws_vpc_ids`** — string[].

Allowed AWS VPC endpoint IDs (vpce-...). Connections must come through one of these endpoints. Use an empty array to remove all entries. Leave omitted to keep the current setting.

Example:

```
["vpce-0fe6c8807461bba49"]

```
