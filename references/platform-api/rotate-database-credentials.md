# Rotate database credentials

[Context index](database-lifecycle-api-workflows.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "could not find database with name [databaseName]: record not found"
}
```

POST /v1/organizations/{organizationSlug}/databases/{databaseName}/auth/rotate

Applying these changes requires brief downtime.

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate using the Bearer header format `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the relevant organization or user account.

**`databaseName`**: string; required.

The name of the database.

#### Response

Successful response (No Content)
