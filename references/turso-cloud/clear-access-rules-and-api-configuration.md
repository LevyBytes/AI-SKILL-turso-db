# Clear access rules and API configuration

[Context index](access-durability-encryption-and-migration.md) · [Setup and preceding context](ip-and-vpc-access-rules.md)

### Clear Rules

 Delete every allow rule so connections are open from any source:

```
turso db config allow-rules clear my-db

```

 Empty one list without modifying the other list:

```
# Clear only the IP list
turso db config allow-rules clear my-db --ips

# Clear only the VPC list
turso db config allow-rules clear my-db --aws-vpcs

```

### API

 Manage allow rules with the database [configuration endpoint](https://docs.turso.tech/api-reference/databases/update-configuration). Include `allowed_ips` and/or `allowed_aws_vpc_ids` in the request body:

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "allowed_ips": ["203.0.113.7", "10.0.0.0/8"],
    "allowed_aws_vpc_ids": ["vpce-0fe6c8807461bba49"]
  }'

```

 To empty a list, send an explicit empty array:

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "allowed_ips": [],
    "allowed_aws_vpc_ids": []
  }'

```

 Leave a field out of the request body to keep that list as it is.
