# Http, Introduction

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Includes: Quickstart, Base URL, and Which package should I use?.

## Quickstart

This quickstart walks through how to:
- Connect your app to a remote Turso database
- Run SQL queries through [SQL over HTTP](https://github.com/tursodatabase/libsql/blob/main/docs/HRANA_3_SPEC.md#hrana-over-http)

|  | `@tursodatabase/serverless` | `@libsql/client` | Raw HTTP |
| --- | --- | --- | --- |
| **Status** | Production-ready | Production-ready | Always available |
| **Dependencies** | Uses only `fetch` — zero native dependencies | Requires Node.js or `/web` subpath | None (any HTTP client) |
| **ORM support** | Not yet supported | Drizzle, Prisma, and others | N/A |

 **`@tursodatabase/serverless`** has no native dependency footprint and is the smallest choice. Choose **`@libsql/client`** when you want a mature driver now, including ORM integration.
-  @tursodatabase/serverless
-  @libsql/client
-  Raw HTTP
The `@tursodatabase/serverless` package reaches Turso through the `fetch` API only, with no native dependencies, so it stays the smallest fit for edge and serverless runtimes.1

Install

```
npm install @tursodatabase/serverless

```

2

Connect and query

```
import { connect } from "@tursodatabase/serverless";

const conn = await connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const stmt = conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt.get([123]);

```

For libSQL client API compatibility, import the compat module:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const result = await client.execute("SELECT * FROM users WHERE id = ?", [123]);

```

`@libsql/client` is Turso's production-ready libSQL driver across the ecosystem. In serverless or edge runtimes, import `/web`.1

Install

```
npm install @libsql/client

```

2

Connect and query

```
import { createClient } from "@libsql/client/web";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN!,
});

const result = await client.execute({
  sql: "SELECT * FROM users WHERE id = ?",
  args: [123],
});

```

For edge and serverless runtimes, import `@libsql/client/web` rather than `@libsql/client`. The `/web` entrypoint uses the HTTP protocol. You can also call the HTTP API directly from any HTTP client.1

Create HTTP Database URL

Fetch the database URL with the [Turso CLI](https://docs.turso.tech/cli) or the [Platform API](https://docs.turso.tech/api-reference):

```
turso db show <database-name> --http-url

```

Add `/v2/pipeline` to the URL, then proceed.2

Create Database Auth Token

Create a fresh auth token for the database with the Turso CLI or Platform API:

```
turso db tokens create <database-name>

```

3

Create JSON Request Payload

Because the request body is JSON, build a JSON payload that runs a SQL statement and then closes the connection immediately:

```
{
  "requests": [
    { "type": "execute", "stmt": { "sql": "SELECT * FROM users" } },
    { "type": "close" }
  ]
}

```

Change `stmt.sql` to query a table that already exists. Bound parameter examples appear on the [Reference Page](https://docs.turso.tech/sdk/http/reference#parameter-binding).4

Execute HTTP Request

Use an HTTP client library for your language to send the request to the URL you created. Set the `Authorization` header to the token, and send a JSON body containing the SQL statement. Append the pipeline path that accepts requests, `/v2/pipeline`, to the Base URL.

```
const url = "https://[databaseName]-[organizationSlug].turso.io/v2/pipeline";
const authToken = "...";

fetch(url, {
  method: "POST",
  headers: {
    Authorization: `Bearer ${authToken}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    requests: [
      { type: "execute", stmt: { sql: "SELECT * FROM users" } },
      { type: "close" },
    ],
  }),
})
  .then((res) => res.json())
  .then((data) => console.log(data))
  .catch((err) => console.log(err));

```

Response

The response is a JSON object whose `results` array holds query output, similar to this:

```
{
  "baton": null,
  "base_url": null,
  "results": [
    {
      "type": "ok",
      "response": {
        "type": "execute",
        "result": {
          "cols": [],
          "rows": [],
          "affected_row_count": 0,
          "last_insert_rowid": null,
          "replication_index": "1"
        }
      }
    },
    {
      "type": "ok",
      "response": {
        "type": "close"
      }
    }
  ]
}

```

Placeholders can be used for values when needed:

```
{
  "type": "execute",
  "stmt": {
    "sql": "SELECT * FROM users WHERE id = ?",
    "args": [
      {
        "type": "integer",
        "value": "1"
      }
    ]
  }
}

```

For each arg, `type` maps to the column datatype and must be one of `null`, `integer`, `float`, `text`, or `blob`. JSON carries `value` as a `String` so precision is not lost when JSON implementations represent numbers as 64-bit floats.

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

#### Parameter binding

 Bound-parameter queries are available in two forms:
1. Positional query parameters are matched by their order in the parameter list and use the `?` prefix. For positional parameters, send the values as an array in `args`.

```
{
  "requests": [
    {
      "type": "execute",
      "stmt": {
        "sql": "SELECT * FROM users WHERE name = ?",
        "args": [
          {
            "type": "text",
            "value": "Turso"
          }
        ]
      }
    },
    {
      "type": "close"
    }
  ]
}

```

1. Named bound parameters are referenced by name and start with `:`, `@`, or `$`. For named parameters, provide `named_args` as an array of objects that map each parameter to its value.

```
{
  "requests": [
    {
      "type": "execute",
      "stmt": {
        "sql": "SELECT * FROM users WHERE name = :name OR name = $second_name OR name = @third_name",
        "named_args": [
          {
            "name": "name",
            "value": {
              "type": "text",
              "value": "Turso"
            }
          },
          {
            "name": "second_name",
            "value": {
              "type": "text",
              "value": "Not Turso"
            }
          },
          {
            "name": "third_name",
            "value": {
              "type": "text",
              "value": "Maybe Turso"
            }
          }
        ]
      }
    },
    {
      "type": "close"
    }
  ]
}

```

 Each `named_args` object's `name` property may include the prefix or leave it off; the example above leaves prefixes out, and both forms work. For each arg, `type` maps to the column datatype and must be one of `null`, `integer`, `float`, `text`, or `blob`. When using `blob`, replace `value` with `base64` and encode the argument as `base64` before sending the request. JSON stores `value` as a `String` to preserve precision when JSON implementations model all numbers as 64-bit floats.

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
      "stmt": { "sql": "INSERT INTO users VALUES (\"iku\")" }
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
| `baton` | string | The baton identifies the server-side connection so it can be reused. |
| `base_url` | string | The server base URL that handled the request. Reuse this URL later to route traffic back to that server. |
| `results` | array | Results corresponding to every request in the pipeline. |

 The `results` array holds one result for each pipeline request. A result includes these fields:
| Field | Type | Description |
| --- | --- | --- |
| `cols` | array | Columns included in the returned rows. |
| `rows` | array | Rows produced by the query. |
| `affected_row_count` | integer | How many rows the query affected. |
| `last_insert_rowid` | integer | Last inserted row ID. |
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

### Schema Migration Status

 Turso offers a managed multi-tenant schema system called [Multi-DB Schemas](https://docs.turso.tech/features/multi-db-schemas). It lets one parent database automatically share its schema with related child databases, and parent schema changes propagate to those databases. Use `/v1/jobs` to track schema migration status. This is currently unavailable on AWS for Free, Developer and Scaler plans.

### `GET /v1/jobs`

 Returns a summary for every schema migration job:

```
curl -X GET 'https://[databaseName]-[organizationSlug].turso.io/v1/jobs' \
     -H 'Authorization: Bearer TOKEN'

```

#### Response Fields

 schema_version number Version currently applied to the schema. migrationsarrayList of migration jobs

Show Migration Object

job_id number Migration job unique ID. statusstringCurrent job state. Possible values: `WaitingDryRun`, `DryRunSuccess`, `DryRunFailure`, `WaitingRun`, `RunSuccess`, `RunFailure`

### `GET /v1/jobs/:id`

 Returns details for one migration job.

```
curl -X GET 'https://[databaseName]-[organizationSlug].turso.io/v1/jobs/:id' \
     -H 'Authorization: Bearer TOKEN'

```

#### Path Parameters

 idnumberrequiredThe migration job ID.

#### Response Fields

 job_id number Migration job unique ID. statusstringOverall job state. Possible values match the `/v1/jobs` endpoint. errorstringError message when the job failed, otherwise null. progressarrayList of per-database migration states.

Show Progress Object

namespacestringName of the database. statusstringStatus for this database migration. Possible values: `Enqueued`, `DryRunSuccess`, `DryRunFailure`, `Run`, `Success`, `Failure` errorstringError message if this database migration failed, otherwise null.

### Listen to changes

 Listen for changes committed to the database through `/beta/listen`: This feature is in technical preview. Report problems via [Join us in Discord](https://tur.so/discord). It is currently unavailable on AWS for Free, Developer and Scaler plans. The database group must run `v0.24.18` or later - `turso group update <group-name> --version latest`

### `GET /beta/listen`

 Request

```
curl -L 'https://[primary-instance-id]-[databaseName]-[organizationSlug].turso.io/beta/listen?table=TABLE_NAME' \
-H 'Authorization: Bearer TOKEN'

```

#### Query Parameters

 tablestringrequiredThe table to listen to. actionstringrequiredThe action to listen for - `insert`, `update`, or `delete`.

## Which package should I use?

### Which package should I use?

| Use case | TypeScript | Python | Go | Rust |
| --- | --- | --- | --- | --- |
| **Local database** (embedded, device-local, offline) | `@tursodatabase/database` | `pyturso` | `tursogo` | `turso` |
| **Local database + cloud sync** (push/pull) | `@tursodatabase/sync` | `pyturso` (with sync) | `tursogo` (with sync) | `turso` (with `sync` feature) |
| **Remote access** (servers, Docker, serverless, edge - any networked connection) | `@tursodatabase/serverless` | `libsql` | `libsql-client-go` | `libsql` (with `remote` feature) |
| **libSQL** — ORM support (Drizzle, Prisma), production-ready, battle-tested | `@libsql/client` | `libsql` | `go-libsql` | `libsql` |

 **Starting a new project?** For TypeScript, Python, Go, and Rust, begin with `@tursodatabase/database`, `pyturso`, `tursogo`, or `turso`. Those packages run on the Turso Database engine, a SQLite-compatible rewrite that provides concurrent writes, async I/O, and local-first sync. **Need sync?** Choose [Turso Sync](https://docs.turso.tech/sync/usage) when reads and writes should happen locally and cloud exchange should be explicit through `push()` / `pull()`. **Using an ORM?**
- **TypeScript** - Drizzle and Prisma use `@libsql/client`, and the major ORMs in the ecosystem provide production-ready support.
- **Rust** - [Toasty](https://docs.turso.tech/sdk/rust/orm/toasty), Tokio project's async ORM, includes a native Turso driver based on the `turso` crate.
- **Python** - refer to the [SQLAlchemy guide](https://docs.turso.tech/sdk/python/orm/sqlalchemy).
 **Migrating from SQLite?** Turso Database can replace SQLite without changing existing SQL, schema, or queries. **Already using `@libsql/client`, `libsql`, or `go-libsql`?** These packages use [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork behind Turso Cloud today, and are mature production options. If the workload requires concurrent writes, push/pull sync, or local-first writes for offline, multi-writer, or bidirectional scenarios, the Turso Database packages may fit better; choose based on workload needs.

### Why multiple packages in TypeScript?

 Keeping `@tursodatabase/database` separate from `@tursodatabase/serverless` keeps bundles small. Because `@tursodatabase/serverless` depends only on `fetch` and has no native dependency, it can target Node.js, Docker containers, edge runtimes, browsers, and serverless functions. **Need sync?** New projects should use [Turso Sync](https://docs.turso.tech/sync/usage) when they want local reads and writes with deliberate `push()` / `pull()` cloud exchange. The libSQL-based SDKs below can also use Embedded Replicas, where reads happen locally and writes go to the cloud primary.

### Official SDKs

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### Community SDKs

 These community-maintained SDKs are not officially supported by Turso.

### Flutter / Dart

### Capacitor

### React Native / OP-SQLite

### Laravel

### .NET

### Java

### Stateless libSQL

### SQL over HTTP

### SQL over HTTP
