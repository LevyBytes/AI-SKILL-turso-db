# Retrieve database details

[Context index](database-lifecycle-api-workflows.md)

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

GET /v1/organizations/{organizationSlug}/databases/{databaseName}

#### Authorizations

**`Authorization`**: string; header; required.

Include Bearer authentication with `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug of the organization or user account.

**`databaseName`**: string; required.

The name of the database.

#### Response

Successful response

**`database`**: object.

Open when you need the nested `database` contract: [field details](database-response-fields.md).
