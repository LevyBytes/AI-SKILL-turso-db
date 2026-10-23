# List databases and filter results

[Context index](database-lifecycle-api-workflows.md)

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

GET /v1/organizations/{organizationSlug}/databases

#### Authorizations

**`Authorization`**: string; header; required.

Use a Bearer authentication header in the form `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug of the target organization or user account.

#### Query Parameters

**`group`**: string.

Filter databases by group name.

**`schema`**: string.

Schema database name used to retrieve databases under that parent schema.

**`parent`**: string.

Limit database branch results by parent database ID.

#### Response

200 - application/json

Successful response

**`databases`**: object[].

Open when you need the nested `databases` contract: [field details](database-response-fields.md).
