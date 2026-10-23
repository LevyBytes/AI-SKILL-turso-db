# Retrieve database query statistics

[Context index](database-lifecycle-api-workflows.md)

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/stats' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "top_queries": [
    {
      "query": "SELECT COUNT(*), CustomerID FROM Orders GROUP BY CustomerID HAVING COUNT(*) > 5;",
      "rows_read": 123,
      "rows_written": 4567
    }
  ]
}
```

GET /v1/organizations/{organizationSlug}/databases/{databaseName}/stats

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate through a Bearer header formatted as `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization or user account slug.

**`databaseName`**: string; required.

The name of the database.

#### Response

Successful response

**`top_queries`**: object[] | null.

Top queries run against the specified database, plus totals for rows read and rows written.

Open when you need the nested `top_queries` contract: [field details](database-query-usage-fields.md).
