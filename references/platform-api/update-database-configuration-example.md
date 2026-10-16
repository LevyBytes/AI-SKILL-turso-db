# Update database configuration example

[Context index](database-configuration-upload-and-usage.md) · [Continue](update-database-configuration-request-fields.md)

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

PATCH /v1/organizations/{organizationSlug}/databases/{databaseName}/configuration
