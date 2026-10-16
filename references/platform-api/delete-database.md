# Delete database

[Context index](database-lifecycle-api-workflows.md)

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "database": "my-db"
}
```

DELETE /v1/organizations/{organizationSlug}/databases/{databaseName}

#### Authorizations

**`Authorization`** — string; header; required.

Send the Bearer authentication header as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Organization or user account slug value.

**`databaseName`** — string; required.

The name of the database.

#### Response

Successful response

**`database`** — string.

Name of the deleted database.

Example:

`"my-db"`
