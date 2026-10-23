# List database instances

[Context index](database-lifecycle-api-workflows.md)

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

GET /v1/organizations/{organizationSlug}/databases/{databaseName}/instances

#### Authorizations

**`Authorization`**: string; header; required.

Pass Bearer authentication as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the organization or user account being addressed.

**`databaseName`**: string; required.

The name of the database.

#### Response

200 - application/json

Successful response

**`instances`**: object[].

Open when you need the nested `instances` contract: [field details](database-instance-response-fields.md).
