# HTTP transactions results and service endpoints

[Context index](http-api-and-package-selection.md) · [Setup and preceding context](http-authentication-and-pipeline-lifecycle.md) · [Previous phase](http-positional-and-named-parameters.md) · [Continue](http-migration-status-and-change-listeners.md)

#### Interactive query

 For some workflows, several operations need to run on one connection across multiple roundtrips. Leave the connection open to do that:

```
{
  "requests": [{ "type": "execute", "stmt": { "sql": "BEGIN" } }]
}

```

 Because the connection was left open, the response includes a `baton`. That baton can be sent with later queries on the same connection: Body

```
{
  "baton": "m7lVVgEvknpf1P1irxHsHqrAqH7BLiwO4DQIAwr93PdZWGvdBNugLSokSsCZNkry",
  "requests": [
    { "type": "execute", "stmt": { "sql": "CREATE TABLE users (name)" } },
    {
      "type": "execute",
      "stmt": { "sql": "INSERT INTO users VALUES ('iku')" }
    },
    { "type": "execute", "stmt": { "sql": "COMMIT" } },
    { "type": "close" }
  ]
}

```

 Connections and transactions both expire. A transaction must finish within 5 seconds, and an idle connection closes after 10 seconds.

#### Response types

 The response includes these fields:

| Field | Type | Description |
| --- | --- | --- |
| `baton` | string or null | The baton identifies the server-side connection so it can be reused. |
| `base_url` | string or null | The server base URL that handled the request. Reuse this URL later to route traffic back to that server. |
| `results` | array | Results corresponding to every request in the pipeline. |

 Each `results` entry is an `ok` envelope containing `response`, or an `error` envelope containing `error`. An execute response places the following fields under `response.result`; a close response does not have a row result. Some server statistics are extensions to the base protocol.

| Field | Type | Description |
| --- | --- | --- |
| `cols` | array | Columns included in the returned rows. |
| `rows` | array | Rows produced by the query. |
| `affected_row_count` | integer | How many rows the query affected. |
| `last_insert_rowid` | decimal string or null | Last inserted row ID. |
| `replication_index` | string | Replication timestamp used when this query ran. |
| `rows_read` | integer | How many rows the query read. |
| `rows_written` | integer | How many rows the query wrote. |
| `query_duration_ms` | float | Query duration, measured in milliseconds. |

### `GET /version`

 Use `/version` to read the current version of the server that runs your database:

```
curl -L -X GET 'https://[databaseName]-[organizationSlug].turso.io/version' \
  -H 'Authorization: Bearer TOKEN'

```

### `GET /health`

 Use `/health` to check database health; it returns no body and an HTTP status:

```
curl -L -X GET 'https://[databaseName]-[organizationSlug].turso.io/health' \
  -H 'Authorization: Bearer TOKEN'

```

### `GET /dump`

 Use `/dump` to dump the database:

```
curl -L -X GET 'https://[databaseName]-[organizationSlug].turso.io/dump' \
  -H 'Authorization: Bearer TOKEN'

```

 With [multi-db schemas](https://docs.turso.tech/features/multi-db-schemas) or vector databases, the dump includes statements for internally managed tables.
