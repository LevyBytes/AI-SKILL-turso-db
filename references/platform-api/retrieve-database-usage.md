# Retrieve database usage

[Context index](database-configuration-upload-and-usage.md)

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/usage?from=2023-01-01T00:00:00Z&to=2023-02-01T00:00:00Z' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "database": {
    "uuid": "<unknown>",
    "instances": [
      {
        "uuid": "cd831986-94e5-11ee-a6fe-7a52e1f7759a",
        "usage": {
          "rows_read": 0,
          "rows_written": 0,
          "storage_bytes": 4096,
          "bytes_synced": 0
        }
      },
      {
        "uuid": "0be90471-6906-11ee-8553-eaa7715aeaf2",
        "usage": {
          "rows_read": 0,
          "rows_written": 0,
          "storage_bytes": 4096,
          "bytes_synced": 0
        }
      }
    ],
    "total": {
      "rows_read": 0,
      "rows_written": 0,
      "storage_bytes": 8192,
      "bytes_synced": 0
    }
  }
}
```

GET /v1/organizations/{organizationSlug}/databases/{databaseName}/usage

#### Authorizations

**`Authorization`**: string; header; required.

Use a Bearer auth header formatted as `Bearer <token>`, with `<token>` set to your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization or user account slug.

**`databaseName`**: string; required.

The name of the database.

#### Query Parameters

**`from`**: string&lt;date-time&gt;.

Start datetime for usage retrieval, provided in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. If omitted, the current calendar month is used. Example: `2023-01-01T00:00:00Z`

**`to`**: string&lt;date-time&gt;.

End datetime for usage retrieval, provided in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. If omitted, the current calendar month is used. Example: `2023-02-01T00:00:00Z`

#### Response

Successful response

**`database`**: object.

Database usage object that includes total usage and per-instance usage for rows read and rows written, plus total storage size in bytes.

Open when you need the nested `database` contract: [field details](database-usage-response-fields.md).
