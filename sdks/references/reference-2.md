# Ts — Reference

> Sdks reference. Original prose; identifiers preserved verbatim.

Turso offers three TypeScript packages:
|  | `@tursodatabase/database` | `@tursodatabase/sync` | `@tursodatabase/serverless` | `@libsql/client` |
| --- | --- | --- | --- | --- |
| **Use case** | Local / embedded database | Local database + cloud sync | Remote access (servers, containers, serverless, edge) | ORM support (Drizzle, Prisma) |
| **Engine** | Turso Database (rewrite) | Turso Database (rewrite) | Turso Database | libSQL (SQLite fork) |
| **Dependencies** | Native (Node.js, WASM) | Native (Node.js) | `fetch` only — zero native deps | Requires Node.js or `/web` subpath |
| **Concurrent writes** | Yes (MVCC) | Yes (MVCC) | Planned | Not supported |
| **Sync** | — | push/pull (local-first) | — | Embedded Replicas, with write traffic routed to the cloud primary |
| **ORM support** | Drizzle (beta) | — | — | Drizzle, Prisma, and others |

 **Starting a new project?** Choose `@tursodatabase/database` for local/embedded use, `@tursodatabase/sync` when local storage also needs cloud sync, or `@tursodatabase/serverless` for applications that connect to a remote Turso Cloud database, including Node.js servers, Docker containers, serverless functions, and edge runtimes. **Using an ORM?** Select `@libsql/client`; it is production-ready and supported by Drizzle, Prisma, and other integrations. Drizzle also offers [beta support](https://orm.drizzle.team/docs/connect-turso-database) for `@tursodatabase/database` in local/embedded scenarios. These runtime environments are known to work:
- Node.js version 12 or later
- Deno
- CloudFlare Workers
- Netlify & Vercel Edge Functions

### @tursodatabase/database

 Use this package for local or embedded databases. It runs on the Turso Database engine and provides concurrent writes (MVCC) plus async I/O.

### Installing

```
npm install @tursodatabase/database

```

### Initializing

```
import { connect } from "@tursodatabase/database";

const db = await connect("app.db");

```

 Databases that live entirely in memory are supported too:

```
const db = await connect(":memory:");

```

### Querying

```
const stmt = db.prepare("SELECT * FROM users");
const users = await stmt.all();

const insert = db.prepare("INSERT INTO users (username) VALUES (?)");
await insert.run("alice");

```

### Encryption

 To protect local databases at rest, pass the `encryption` option:

```
import { connect } from "@tursodatabase/database";

const db = await connect("encrypted.db", {
  encryption: {
    cipher: "aegis256",
    hexkey: "b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327",
  },
});

```

 Available ciphers are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, `aes128gcm`. When a database is encrypted, it is not readable as a normal SQLite database; open it through the Turso Database engine. Turso Cloud also supports bring-your-own-key encryption for cloud databases; [learn more](https://docs.turso.tech/cloud/encryption).

### @tursodatabase/sync

 Use this when a local database needs cloud sync. Reads and writes stay local, then `push()` sends local changes up and `pull()` brings remote changes down.

### Installing

```
npm install @tursodatabase/sync

```

### Initializing

```
import { connect } from "@tursodatabase/sync";

const db = await connect({
  path: "./app.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

 During its first run, the local database is bootstrapped automatically from the remote. Full behavior is covered in [Turso Sync](https://docs.turso.tech/sync/usage).

### Push and Pull

```
// Push local writes to Turso Cloud
await db.push();

// Pull remote changes to local database
const changed = await db.pull();

```

### Checkpoint

 Use checkpointing to keep the local WAL compact and disk growth bounded without losing sync state:

```
await db.checkpoint();

```

### Stats

```
const s = await db.stats();
console.info({
  cdcOperations: s.cdcOperations,
  mainWalSize: s.mainWalSize,
  networkReceivedBytes: s.networkReceivedBytes,
  networkSentBytes: s.networkSentBytes,
  lastPullUnixTime: s.lastPullUnixTime,
  lastPushUnixTime: s.lastPushUnixTime,
  revision: s.revision,
});

```

### @tursodatabase/serverless

 Use this package for any app that connects to a remote Turso Cloud database, including Node.js servers, Docker containers, serverless functions such as AWS Lambda and Vercel Functions, and edge runtimes such as Cloudflare Workers and Deno Deploy. It relies only on `fetch`, has no native dependencies, and runs wherever `fetch` exists.

### Installing

```
npm install @tursodatabase/serverless

```

### Initializing

```
import { connect } from "@tursodatabase/serverless";

const conn = connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

 Use the compat module when you need behavior aligned with the `@libsql/client` API:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Querying

```
const stmt = await conn.prepare("SELECT * FROM users");
const rows = await stmt.all();

const stmt2 = await conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt2.get([1]);

```

### @libsql/client

 The `@libsql/client` package uses [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork behind Turso Cloud today. It is production-ready and well exercised in real deployments, and it fits cases that require ORM support outside Drizzle, such as Prisma, or projects that already depend on a `@libsql/client` codebase. In `@libsql/client` Embedded Replicas, reads happen locally; writes go to the cloud primary and later appear on the replica. Embedded Replicas are fully supported. For new sync-focused builds, use `@tursodatabase/sync` together with [Turso Sync](https://docs.turso.tech/sync/usage).

### Installing

 Start by adding the `@libsql/client` dependency to your project:

```
npm install @libsql/client

```

### Initializing

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

 When working with local libsql or an sqlite file, `authToken` does not need to be provided.

### In-Memory Databases

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: ":memory:",
});

```

### Local Development

 For local development, pass an SQLite file path into `createClient`:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  authToken: "...",
});

```

 `@libsql/client/web` cannot use local file URLs.

### Embedded Replicas

 If your workload requires **offline writes**, **bidirectional sync**, or **multi-writer convergence**, prefer `@tursodatabase/sync`: reads and writes both happen locally, and synchronization is explicit through `push()` / `pull()`. To use embedded replicas, pass the Turso Database URL as `syncUrl`:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  syncUrl: "libsql://[databaseName]-[organizationSlug].turso.io",
  authToken: "...",
});

```

 Embedded Replicas require an environment where the file system is accessible.

#### Manual Sync

```
await client.sync();

```

#### Periodic Sync

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  syncUrl: "libsql://[databaseName]-[organizationSlug].turso.io",
  syncInterval: 60,
  authToken: "...",
});

```

### Encryption

 For new builds that need local encryption, prefer `@tursodatabase/database`; it runs on the Turso Database engine and adds stronger performance plus concurrent write support. TypeScript

```
import { createClient } from "@libsql/client";

const db = createClient({
  url: "file:encrypted.db",
  encryptionKey: process.env.ENCRYPTION_KEY,
});

```

 An encrypted database looks like raw bytes and is not readable as a normal SQLite database. All access must go through the libSQL client; [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

### Concurrency

 Unless configured differently, the client allows as many as `20` requests to run concurrently:

```
import { createClient } from "@libsql/client";

const client = createClient({
  concurrency: 10,
});

```

### Response

 The methods that follow each resolve to a `Promise<ResultSet>`:
| Property | Type | Description |
| --- | --- | --- |
| `rows` | `Array<Row>` | Array of Row objects with row values; empty when the operation is a write |
| `columns` | `Array<string>` | Array of strings naming the columns in the same order as each Row; empty when the operation is a write |
| `rowsAffected` | `number` | The count of rows changed by a write statement, or `0` for non-write results |
| `lastInsertRowid` | `bigint | undefined` | New row ID when a row was inserted; otherwise `undefined` |

### Simple query

 Pass either a string or an object to `execute()` when running a SQL statement:

```
const result = await client.execute("SELECT * FROM users");

```

### Placeholders

 libSQL SQL statements may use both positional placeholders and named placeholders:

```
const result = await client.execute({
  sql: "SELECT * FROM users WHERE id = ?",
  args: [1],
});

const result = await client.batch(
  [
    {
      sql: "INSERT INTO users VALUES (?)",
      args: ["Iku"],
    },
  ],
  "write",
);

```

 For named placeholders, libSQL accepts the same prefix characters SQLite does: `:`, `@` and `$`.

### Transaction Modes

| Mode | SQLite command | Description |
| --- | --- | --- |
| `write` | `BEGIN IMMEDIATE` | This mode permits statements that read and write data. If the transaction writes on a replica, it is sent to the primary instance and cannot run concurrently. |
| `read` | `BEGIN TRANSACTION READONLY` | This mode permits only read statements (select). Read transactions may run on replicas and can proceed in parallel with other read transactions. |
| `deferred` | `BEGIN DEFERRED` | The transaction begins as read-only and switches to write mode when the first write statement runs. That transition can fail when the primary already has an active write transaction. |

### Batch Transactions

 A batch is a sequence of SQL statements run in order inside an implicit transaction. The backend manages the transaction boundary: when everything succeeds, all changes commit; if anything fails, the whole batch rolls back and leaves no changes.

```
const result = await client.batch(
  [
    {
      sql: "INSERT INTO users VALUES (?)",
      args: ["Iku"],
    },
    {
      sql: "INSERT INTO users VALUES (?)",
      args: ["Iku 2"],
    },
  ],
  "write",
);

```

### Interactive Transactions

 SQLite interactive transactions keep a group of read and write operations consistent within one transaction scope. They let you choose when to commit or roll back, and those changes remain isolated from other client work.
| Method | Description |
| --- | --- |
| `execute()` | Like `execute()`, but executed inside the active transaction context |
| `commit()` | Commits every write statement in the transaction |
| `rollback()` | Rolls back the entire transaction |
| `close()` | Immediately stops the transaction |

```
try {
  const userId = "user123";
  const withdrawalAmount = 500;

  const transaction = await client.transaction("write");

  const balanceResult = await transaction.execute({
    sql: "SELECT balance FROM accounts WHERE userId = ?",
    args: [userId],
  });

  const currentBalance = balanceResult.rows[0]["balance"] as number;

  if (currentBalance >= withdrawalAmount) {
    await transaction.execute({
      sql: "UPDATE accounts SET balance = balance - ? WHERE userId = ?",
      args: [withdrawalAmount, userId],
    });
  } else {
    console.log("Insufficient funds");
    await transaction.rollback();
    return;
  }

  await transaction.commit();
} catch (e) {
  console.error(e);
}

```

 In libSQL, interactive transactions hold the database write lock until commit or rollback, and use a 5-second timeout. They may hurt performance on databases that are busy or high-latency.

### ATTACH

 Attach additional databases to the current connection with the `ATTACH` attachment:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const txn = await db.transaction("read");

await txn.execute('ATTACH "<database-id>" AS attached');

const rs = await txn.execute("SELECT * FROM attached.users");

```

 Before using this, [allow `ATTACH`](https://docs.turso.tech/cli/db/config/attach/allow) and create a token authorized to attach a database; [learn more](https://docs.turso.tech/features/attach-database)
