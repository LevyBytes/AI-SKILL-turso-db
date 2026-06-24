# Connect, Integrations

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Dart; Go; Java; Quickstart; Python; Rust; SQL over HTTP.

## Dart

1

Install

Add the turso_dart dependency entry to your `pubspec.yaml` file:

```
dependencies:
  turso_dart:

```

Then run:

```
dart pub get

```

2

Connect

Create a client connection. The target can be an in-memory database or a local database file:

```
import 'package:turso_dart/turso_dart.dart';

// In memory
final client = TursoClient.memory();

// Or local file
final client = TursoClient.local('/path/to/local.db');

await client.connect();

```

3

Create table

Create a table for customers:

```
await client.execute(
  "CREATE TABLE IF NOT EXISTS customers (id INTEGER PRIMARY KEY, name TEXT)"
);

```

4

Insert data

Write a few rows into the customers table:

```
await client.query("INSERT INTO customers(name) VALUES ('John Doe')");
await client.query("INSERT INTO customers(name) VALUES ('Jane Smith')");

```

5

Query data

Read all rows from the customers table:

```
final result = await client.query("SELECT * FROM customers");
print(result);

```

6

Prepared statements

Use prepared statements when you want safer parameter handling and better repeated-query performance:

```
final statement = await client.prepare("SELECT * FROM customers WHERE id = ?");
final result = await statement.query(positional: [1]);
print(result);

```

## Go

1

Install

Add the Turso package to the Go module for your project:

```
go get turso.tech/database/tursogo

```

2

Connect

Connect to a local SQLite database with code like this:

```
package main

import (
    "database/sql"
    "fmt"
    _ "turso.tech/database/tursogo"
)

func main() {
    db, err := sql.Open("turso", "sqlite.db")
    if err != nil {
        panic(err)
    }
    defer db.Close()
}

```

3

Create table

Create a table for users:

```
_, err = db.Exec(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
`)
if err != nil {
    panic(err)
}

```

4

Insert data

Add rows to the users table:

```
_, err = db.Exec("INSERT INTO users (username) VALUES (?)", "alice")
if err != nil {
    panic(err)
}

_, err = db.Exec("INSERT INTO users (username) VALUES (?)", "bob")
if err != nil {
    panic(err)
}

```

5

Query data

Read every row from the users table:

```
rows, err := db.Query("SELECT * FROM users")
if err != nil {
    panic(err)
}
defer rows.Close()

for rows.Next() {
    var id int
    var username string
    if err := rows.Scan(&id, &username); err != nil {
        panic(err)
    }
    fmt.Printf("User: ID: %d, Username: %s\n", id, username)
}

```

## Java

1

Install

The package has not been published to Maven Central yet. To use it for now, build the JAR locally and publish it into your local Maven repository:

```
cd bindings/java

# Please select the appropriate target platform, currently supports `macos_x86`, `macos_arm64`, `windows` and `linux_x86`
make macos_x86

# deploy to maven local
make publish_local

```

Then declare the dependency this way:

```
dependencies {
    implementation("tech.turso:turso:0.0.1-SNAPSHOT")
}

```

## Quickstart

### Quickstart

 1

Install

Add the Turso database package to your JavaScript project:

```
npm i @tursodatabase/database

```

2

Connect

Connect to a local SQLite database with this pattern:

```
import { connect } from "@tursodatabase/database";

const db = await connect("sqlite.db");

```

3

Create table

Create a table for users:

```
const createTable = db.prepare(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
`);
createTable.run();

```

4

Insert data

Insert rows into the users table:

```
const insertUser = db.prepare("INSERT INTO users (username) VALUES (?)");
insertUser.run("alice");
insertUser.run("bob");

```

5

Query data

Read all users from the table:

```
const stmt = db.prepare("SELECT * FROM users");
const users = stmt.all();
console.log(users);

```

### Examples

 Review these JavaScript examples for additional Turso Database usage patterns:
- [Node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-node) - Node.js with a local file database and no sync
- [Wasm + Vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-wasm-vite) - Browser (WASM) using a local database inside the browser

## Python

1

Install

Add the Turso package to your Python project:

```
uv pip install pyturso

```

2

Connect

Connect to a local SQLite database using code like this:

```
import turso

con = turso.connect("sqlite.db")
cur = con.cursor()

```

3

Create table

Create a table for users:

```
cur.execute("""
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
""")
con.commit()

```

4

Insert data

Insert rows into the users table:

```
cur.execute("INSERT INTO users (username) VALUES (?)", ("alice",))
cur.execute("INSERT INTO users (username) VALUES (?)", ("bob",))
con.commit()

```

5

Query data

Read every user from the table:

```
res = cur.execute("SELECT * FROM users")
users = res.fetchall()
print(users)

```

## Rust

1

Install

Add the Turso crate to your Rust project:

```
cargo add turso

```

2

Connect

Connect to a local SQLite database with this example:

```
use turso::Builder;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let db = Builder::new_local("sqlite.db").build().await?;
    let conn = db.connect()?;

    Ok(())
}

```

3

Create table

Create a table for users:

```
conn.execute(
    "CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT NOT NULL
    )",
    ()
).await?;

```

4

Insert data

Insert rows into the users table:

```
conn.execute("INSERT INTO users (username) VALUES (?)", ("alice",)).await?;
conn.execute("INSERT INTO users (username) VALUES (?)", ("bob",)).await?;

```

5

Query data

Read all users from the table:

```
let res = conn.query("SELECT * FROM users", ()).await?;
println!("{:?}", res);

```

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

### API Reference

 **`createDb(name, options?)`** Creates or returns a database instance. If that database does not exist, it is created automatically inside the configured group (`TURSO_GROUP`). Calling `createDb()` again with the same name during one function invocation returns the existing instance.

```
const db = await createDb(process.env.TURSO_DATABASE!, {
  remoteWrites: false,     // Batch writes locally and push on close() (optional, default: true)
});

```

 When `remoteWrites` is `true` (the default), write statements (`INSERT`, `UPDATE`, `DELETE`) go directly to the remote Turso database for immediate durability. When it is `false`, writes are applied to the local SQLite copy and then pushed as one batch when `db.push()` or `db.close()` runs. That mode can help with bulk ingestion or background work where eventual visibility is acceptable. **`db.query(sql, params?)`** Execute a SELECT query and return results.

```
const result = await db.query("SELECT * FROM users WHERE id = ?", [1]);

console.log(result.columns); // ["id", "name", "email"]
console.log(result.rows);    // [[1, "Alice", "alice@example.com"]]

```

 **`db.execute(sql, params?)`** Execute an INSERT, UPDATE, DELETE, or DDL statement. By default, writes go straight to the remote Turso database. If `remoteWrites: false` is configured, writes are made locally and pushed on `close()`.

```
await db.execute("UPDATE users SET name = ? WHERE id = ?", ["Bob", 1]);

```

 **`db.close()`** Close the connection. If `remoteWrites: false` is configured, this also sends any pending local writes to the remote Turso database. Always call it from a `finally` block. As a fallback, the package registers with Vercel's [`waitUntil()`](https://vercel.com/docs/functions/functions-api-reference#waituntil) API so pending changes are pushed if `close()` is not called within 5 seconds after the function completes.

```
const db = await createDb(process.env.TURSO_DATABASE!);
try {
  await db.execute("INSERT INTO users (name) VALUES (?)", ["Charlie"]);
} finally {
  await db.close();
}

```

 **`db.push()`** Push local changes to the remote Turso database manually without closing the connection. This applies only when `remoteWrites: false` is in use.

```
await db.execute("INSERT INTO users (name) VALUES (?)", ["Charlie"]);
await db.push(); // Sync without closing

```

 **`db.pull()`** Fetch the latest changes from the remote Turso database.

```
await db.pull();

```
