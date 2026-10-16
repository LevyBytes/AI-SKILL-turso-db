# HTTP authentication and pipeline lifecycle

[Context index](http-api-and-package-selection.md) · [Continue](http-positional-and-named-parameters.md)

## Base URL

Turso Databases are reachable over HTTP. With the API, developers can run SQL operations through [SQL over HTTP](https://github.com/tursodatabase/libsql/blob/main/docs/HTTP_V2_SPEC.md), read server version details, and check health status. Turso recommends a [native SDK](https://docs.turso.tech/sdk).

### Base URL

 Change the database URL protocol from `libsql://` to `https://`:

```
https://[databaseName]-[organizationSlug].turso.io

```

 Get the database base URL from the Turso CLI:

Get HTTP URL with Turso CLI

```
turso db show <database-name> --http-url

```

### Authentication

 Turso uses Bearer authentication. Pass the API token on every protected request in the `Authorization` header:

```
Authorization: Bearer TOKEN

```

Create token using Turso CLI

```
turso db tokens create <database-name>

```

### Endpoints

 The database exposes these endpoints:

### `POST /v2/pipeline`

 Query a database by calling `/v2/pipeline`. It receives an ordered set of operations for a database connection. Supported operation types:
- `execute`: run a statement on the connection.
- `close`: close the connection.

#### Simple query

```
{
  "requests": [
    { "type": "execute", "stmt": { "sql": "CREATE TABLE users (name)" } },
    { "type": "close" }
  ]
}

```

 Connections remain open until timeout unless the request explicitly closes them, as in the earlier example. Activity on a connection refreshes the timeout. Close the connection after you are done with it.
