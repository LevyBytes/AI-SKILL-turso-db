# Vercel in function SQLite setup

[Context index](client-connection-and-vercel-workflows.md) · [Continue](vercel-database-lifecycle-and-sync-api.md)

### In-Function SQLite

 The `@tursodatabase/vercel-experimental` package is beta software. It can still have bugs or surprising behavior, so use care with production data and keep backups. The `@tursodatabase/vercel-experimental` package copies SQLite database pages into the serverless function itself. Reads use the local copy without network round trips, while writes are sent to the remote database to preserve durability and consistency. This approach works well for read-heavy cases where per-query network latency is the main cost.
- **Per-tenant isolation and sharding** - Give each agent, workspace, or session its own database with separate storage limits
- **Colocation of compute and data** - Run business logic beside the database for zero-hop reads and many queries per request
- **Automatic on-demand provisioning** - Create databases implicitly through `createDb()` without redeploys or binding management
 If you have worked with Turso's embedded replicas or Cloudflare's Durable Objects with SQLite, this design will be familiar.

### Quickstart

 Install the package:

```
npm install @tursodatabase/vercel-experimental

```

 Retrieve your Turso API token and organization slug:

```
turso auth api-tokens mint my-vercel-token
turso org list

```

 Set environment variables on your Vercel project:

```
TURSO_API_TOKEN=your-api-token
TURSO_ORG=your-org-slug
TURSO_GROUP=your-group-name
TURSO_DATABASE=your-database-name

```

 The `TURSO_GROUP` environment variable limits all database operations to one group. Although the API token can access the whole organization, an attacker who controls the database name can reach only databases in the configured group. When possible, keep the database name in a secret environment variable too. Use `createDb()` to open a database and begin querying:

```
import { createDb } from "@tursodatabase/vercel-experimental";

const db = await createDb(process.env.TURSO_DATABASE!);
try {
  // Create tables
  await db.execute(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT NOT NULL,
      email TEXT UNIQUE
    )
  `);

  // Insert data
  await db.execute(
    "INSERT INTO users (name, email) VALUES (?, ?)",
    ["Alice", "alice@example.com"]
  );

  // Query data
  const result = await db.query("SELECT * FROM users");
  console.log(result.rows);
} finally {
  await db.close(); // Syncs and closes the connection
}

```

### Per-tenant databases

 Automatic provisioning makes the database-per-tenant pattern simple. Rather than routing every tenant through one shared database, each user or session receives an isolated SQLite database. Derive the database name from a tenant identifier:

```
// Per-user database
const db = await createDb(`user-${userId}`);

// Per-agent-session database
const db = await createDb(`session-${sessionId}`);

```

 Because the driver creates databases as needed, there is no provisioning step, migration, or connection router to configure. Each tenant's data is isolated at the SQLite level, and every database also receives partial-sync reads.
