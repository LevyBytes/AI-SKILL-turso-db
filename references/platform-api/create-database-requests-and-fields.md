# Create database requests and fields

[Context index](database-lifecycle-api-workflows.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "new-database",
      "group": "default"
  }'

```

```
{
  "database": {
    "DbId": "<unknown>",
    "Hostname": "<unknown>",
    "Name": "<unknown>"
  }
}
```

POST /v1/organizations/{organizationSlug}/databases

#### Authorizations

**`Authorization`**: string; header; required.

Use Bearer authentication in the form `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization or user account slug.

#### Body

application/json

Payload describing the database to be created.

**`name`**: string; required.

Name for the new database. It must use only lowercase letters, numbers, and dashes, with a maximum length of 64 characters.

**`group`**: string; required.

Name of the group where the database will be created. That group must already exist.

**`seed`**: object.

Open when you need the nested `seed` contract: [field details](database-create-fields.md).

**`size_limit`**: string.

Maximum database size in bytes. Values may also include units, for example 1mb, 256mb, 1gb.

**`remote_encryption`**: object.

Database encryption settings. Use them when creating a new encrypted database, creating an encrypted database from an upload (`seed.type = "database_upload"`), or forking from an encrypted database; they are required when the source database is encrypted.

Open when you need the nested `remote_encryption` contract: [field details](database-create-fields.md).

#### Response

Successful response

**`database`**: object.

The newly created database

Open when you need the nested `database` contract: [field details](database-create-fields.md).
