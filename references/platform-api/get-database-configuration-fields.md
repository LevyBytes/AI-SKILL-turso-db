# Get database configuration fields

[Context index](database-lifecycle-api-workflows.md) · [Setup and preceding context](get-database-configuration-example.md)

#### Response

200 - application/json

Successful response

**`size_limit`** — string.

Database size cap in bytes. Unit-suffixed values such as 1mb, 256mb, 1gb are accepted too.

Example:

`"10000"`

**`allow_attach`** — boolean; deprecated.

Controls whether databases may be attached to the current database.

Example:

`true`

**`block_reads`** — boolean.

Current blocked-read status.

Example:

`false`

**`block_writes`** — boolean.

Current blocked-write status.

Example:

`false`

**`delete_protection`** — boolean.

Stops deletion of the database.

Example:

`true`

**`allowed_ips`** — string[].

Allowed IP addresses and CIDR blocks; an empty list leaves IP access unrestricted.

Example:

```
["203.0.113.7", "10.0.0.0/8"]
```

**`allowed_aws_vpc_ids`** — string[].

Allowed AWS VPC endpoint IDs; an empty list leaves VPC endpoint access unrestricted.

Example:

```
["vpce-0fe6c8807461bba49"]
```
