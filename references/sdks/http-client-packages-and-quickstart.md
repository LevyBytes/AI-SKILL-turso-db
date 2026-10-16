# HTTP client packages and quickstart

[Context index](http-api-and-package-selection.md) · [Continue](http-database-url-and-pipeline-request.md)

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
The `@tursodatabase/serverless` package reaches Turso through the `fetch` API only, with no native dependencies, so it stays the smallest fit for edge and serverless runtimes.

### Step 1: Install

```
npm install @tursodatabase/serverless

```

### Step 2: Connect and query

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

`@libsql/client` is Turso's production-ready libSQL driver across the ecosystem. In serverless or edge runtimes, import `/web`.

### Step 1: Install

```
npm install @libsql/client

```

### Step 2: Connect and query

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
