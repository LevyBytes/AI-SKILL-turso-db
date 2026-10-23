# Update database configuration response fields

[Context index](database-configuration-upload-and-usage.md) · [Setup and preceding context](update-database-configuration-example.md) · [Previous phase](update-database-configuration-request-fields.md)

#### Response

200 - application/json

Successful response

**`size_limit`**: string.

Maximum database size, expressed in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb.

Example:

`"10000"`

**`allow_attach`**: boolean; deprecated.

Controls whether databases may be attached to the current database.

Example:

`true`

**`block_reads`**: boolean.

Current blocked-read status.

Example:

`false`

**`block_writes`**: boolean.

Current blocked-write status.

Example:

`false`

**`delete_protection`**: boolean.

Stops deletion of the database.

Example:

`true`

**`allowed_ips`**: string[].

Allowed IP addresses and CIDR blocks. An empty value indicates there is no IP restriction.

Example:

```
["203.0.113.7", "10.0.0.0/8"]

```

**`allowed_aws_vpc_ids`**: string[].

Allowed AWS VPC endpoint IDs. An empty value indicates there is no VPC endpoint restriction.

Example:

```
["vpce-0fe6c8807461bba49"]

```
