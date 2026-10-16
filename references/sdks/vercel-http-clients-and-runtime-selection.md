# Vercel HTTP clients and runtime selection

[Context index](client-connection-and-vercel-workflows.md)

## SQL over HTTP

Turso provides two database-access patterns for Vercel serverless and edge functions:
- **SQL over HTTP** - Sends each query over the network to the database. It is easy to configure and works in every runtime, including edge functions, because it does not require filesystem access. This is the familiar model used by most database clients.
- **In-Function SQLite** - Replicates database pages into the serverless function. Reads run inside the function without network round trips, and writes are sent to the remote database so durability is maintained. Teams familiar with Turso's embedded replicas or Cloudflare's Durable Objects with SQLite will recognize the pattern. See [Bringing SQLite to Vercel Functions](https://turso.tech/blog/serverless) for a deeper explanation.

|  | SQL over HTTP | In-Function SQLite |
| --- | --- | --- |
| **Packages** | `@tursodatabase/serverless`, `@libsql/client` | `@tursodatabase/vercel-experimental` |
| **Best for** | Write-heavy workloads, edge runtimes without filesystem | Read-heavy workloads and per-tenant databases |
| **Read latency** | Network round-trip per query | In-process SQLite reads with zero network hops |
| **Write latency** | Network round-trip | Network round-trip (same) |
| **Multi-tenant** | Shared database | Database-per-tenant setup with automatic on-demand provisioning |
| **Runtime requirements** | `fetch` API only | Ephemeral filesystem (`/tmp`) |
| **ORM support** | Drizzle, Prisma, and others (`@libsql/client`) | Not yet supported |

### SQL over HTTP

 Connect to Turso databases from Vercel serverless and edge runtimes. Add these variables to the Vercel environment:
- `TURSO_DATABASE_URL` - URL for your Turso database
- `TURSO_AUTH_TOKEN` - Auth token for Turso

|  | `@tursodatabase/serverless` | `@libsql/client` |
| --- | --- | --- |
| **Status** | Production-ready | Production-ready |
| **Dependencies** | Uses only `fetch` — zero native dependencies | Requires Node.js or `/web` subpath |
| **Concurrent writes** | Planned | Will not be supported |
| **ORM support** | Not yet supported | Drizzle, Prisma, and others |

 **`@tursodatabase/serverless`** is the smallest option and has no native dependencies; it is also the driver intended to support concurrent writes later. Choose **`@libsql/client`** when you need a mature driver today with ORM integration.

### Quickstart

-  @tursodatabase/serverless
-  @libsql/client
The `@tursodatabase/serverless` package reaches Turso through the `fetch` API only. With no native dependencies, it remains a small fit for edge and serverless runtimes.

```
npm install @tursodatabase/serverless

```

```
import { connect } from "@tursodatabase/serverless";

const conn = connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const stmt = conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt.get([123]);

```

Use the compat module when you need alignment with the libSQL client API:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const result = await client.execute("SELECT * FROM users WHERE id = ?", [123]);

```

The `@libsql/client` package is the production-ready libSQL driver used throughout the Turso ecosystem. For Vercel serverless and edge runtimes, import `/web`:

```
npm install @libsql/client

```

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

For Vercel deployments, use `@libsql/client/web` rather than `@libsql/client`. The `/web` import uses HTTP, which works in edge and serverless runtimes.
