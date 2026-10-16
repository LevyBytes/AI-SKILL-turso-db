# TypeScript local database and sync quickstart

[Context index](typescript-orm-and-client-quickstarts.md)

## Recommended: @tursodatabase/database (Local / Embedded)

This TypeScript quickstart covers the following tasks:
- Install the Turso package
- Connect to a local or remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: @tursodatabase/database (Local / Embedded)

 For local and embedded deployments such as Node.js, Electron, mobile, or IoT, `@tursodatabase/database` is the preferred package. It runs on the Turso Database engine, a from-scratch SQLite rewrite with concurrent writes (MVCC) and async I/O.

### Step 1: Install

```
npm install @tursodatabase/database

```

### Step 2: Connect

```
import { connect } from "@tursodatabase/database";

const db = await connect("app.db");

```

### Step 3: Execute

```
const createTable = await db.prepare(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
`);
await createTable.run();

const insertUser = await db.prepare("INSERT INTO users (username) VALUES (?)");
await insertUser.run("alice");

const stmt = await db.prepare("SELECT * FROM users");
const users = await stmt.all();
console.log(users);

```

### Step 4: Sync (push and pull)

To synchronize a local database with Turso Cloud, use [`@tursodatabase/sync`](https://docs.turso.tech/sync/usage):

```
npm install @tursodatabase/sync

```

```
import { connect } from "@tursodatabase/sync";

const db = await connect({
  path: "./app.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

await db.exec("INSERT INTO users (username) VALUES ('bob')");

// Push local writes to Turso Cloud
await db.push();

// Pull remote changes to local database
const changed = await db.pull();

```

Reads and writes use the local database file, which makes operation quick and available offline. `push()` uploads local changes, while `pull()` retrieves changes from the remote side. For conflict handling, checkpointing, and related behavior, see [Turso Sync](https://docs.turso.tech/sync/usage).
