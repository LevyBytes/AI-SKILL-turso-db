# Get database configuration example

[Context index](database-lifecycle-api-workflows.md) · [Continue](get-database-configuration-fields.md)

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

GET /v1/organizations/{organizationSlug}/databases/{databaseName}/configuration

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate with a Bearer header formatted as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the organization or user account.

**`databaseName`**: string; required.

The name of the database.
