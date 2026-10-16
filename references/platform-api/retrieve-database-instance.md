# Retrieve database instance

[Context index](database-lifecycle-api-workflows.md)

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

GET /v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName}

#### Authorizations

**`Authorization`** — string; header; required.

Set the Bearer authentication header to `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account.

**`databaseName`** — string; required.

The name of the database.

**`instanceName`** — string; required.

Instance name, meaning the location code.

#### Response

200 - application/json

Successful response

**`instance`** — object.

Open when you need the nested `instance` contract: [field details](database-instance-response-fields.md).
