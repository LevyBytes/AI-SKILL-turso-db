# Checkpoint, Conflict Resolution, Local Sync Server, Partial ...

> Database Core reference. Original prose; identifiers preserved verbatim.

## Overview

Included topics: Overview; Last Push Wins; Starting the server; Modes; Offline-first writes.

## Overview

This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account.

### Overview

 The sync engine records local writes in a WAL (Write-Ahead Log). As changes accumulate, that WAL increases in size. A checkpoint moves committed frames into the main database file, then shortens the WAL. For sync databases, auto-checkpoint is **disabled**, so call `checkpoint()` yourself.

### Why Checkpoint Matters

 If you do not checkpoint, the WAL keeps expanding. With enough writes, it may grow far beyond the database file. Running checkpoints frees that storage, and `stats()` can show the effect:

```
const before = await db.stats();
console.log('WAL size before:', before.mainWalSize);

await db.checkpoint();

const after = await db.stats();
console.log('WAL size after:', after.mainWalSize);

```

### When to Checkpoint

 Run `checkpoint()` at intervals that fit your write workload:
- **After bulk inserts** - when a large batch of rows is added, checkpoint afterward so WAL storage is released
- **On a schedule** - for workloads with ongoing writes, run checkpoints regularly, such as every few minutes
- **When WAL size is large** - watch `stats().mainWalSize` and checkpoint once it passes your chosen threshold

```
await db.checkpoint();

```

## Last Push Wins

This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account.

### Last Push Wins

 Turso sync resolves conflicts with a **last push wins** approach. If two clients change the same record and both push, whichever push arrives last sets the remote result.
| Time | Client A | Client B |
| --- | --- | --- |
| T1 | `UPDATE users SET name='Alice' WHERE id=1` |  |
| T2 |  | `UPDATE users SET name='Bob' WHERE id=1` |
| T3 | `push()` |  |
| T4 |  | `push()` |

 **Result**: The final name is `'Bob'`, since Client B performed the later push.

### What Happens During Pull

 If a pull occurs while local edits have not yet been pushed:
1. The local database returns to the most recent synced state
2. Remote changes are applied
3. Those pending local edits are **replayed** afterward
 The rollback followed by replay is atomic; on failure, the database stays at its earlier state.

## Starting the server

The `tursodb` CLI provides an integrated sync server for local use. You can build and test sync flows on your own machine without a Turso Cloud account. Because the local server speaks the same sync protocol as Turso Cloud, application code can target either environment the same way.

### Starting the server

 Start the sync server by passing `--sync-server` and an address. The database argument tells the server where to keep its data:

```
tursodb ./server.db --sync-server 0.0.0.0:8080

```

 This launches a sync server on port `8080` using `./server.db` as its backing database.

### Connecting clients

 Configure the sync client to use `http://localhost:8080`. The local server does not require an auth token.

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './local-replica.db',
  url: 'http://localhost:8080',
});

```

 After connecting, the sync operations (`push`, `pull`, `checkpoint`, `stats`) behave as covered in the [Usage](https://docs.turso.tech/sync/usage) guide.

### Example: full local sync workflow

 This sample shows an end-to-end local workflow: launch the server, write data with one client, then sync it to another. 1

1. Start the sync server

In a terminal, start the server:

```
tursodb ./server.db --sync-server 0.0.0.0:8080

```

2

2. Write from Client A, then push

```
import { connect } from '@tursodatabase/sync';

const clientA = await connect({
  path: './client-a.db',
  url: 'http://localhost:8080',
});

await clientA.exec("CREATE TABLE IF NOT EXISTS notes (id TEXT PRIMARY KEY, body TEXT)");
await clientA.exec("INSERT INTO notes VALUES ('n1', 'hello from client A')");

await clientA.push();

```

3

3. Pull from Client B

```
import { connect } from '@tursodatabase/sync';

const clientB = await connect({
  path: './client-b.db',
  url: 'http://localhost:8080',
});

const changed = await clientB.pull();
console.log('changes pulled:', changed);

const rows = await clientB.query("SELECT * FROM notes");
console.log(rows);
// [{ id: 'n1', body: 'hello from client A' }]

```

## Modes

This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account. Partial sync allows an app to open and query a database before the full file has been downloaded. When a query reaches data missing from the local copy, the client fetches the needed database pages from Turso Cloud on demand. For large databases, this can cut startup latency and network transfer while still working with the same push/pull methods as Turso's standard `sync` solution.
- Reads that touch data not yet present locally cause the needed pages to be fetched automatically.
- Writes continue to land locally first, then sync as logical statements.

### Modes

 At connection time, two bootstrap options determine the initial local contents:
- **Prefix bootstrap**: fetch the first N bytes from the database file.
  - A useful default when the initial local footprint should be small and predictable.

- **Query bootstrap**: fetch the pages reached by executing a server-side SQL query.
  - Best when only a focused working set should be hydrated, such as a single user's rows, small metadata tables, references, etc.

 In either mode, absent pages are still fetched lazily when needed.

### Prefix bootstrap

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',
  url: 'libsql://...',
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
  },
});

```

### Query bootstrap

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',
  url: 'libsql://...',
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: {
      kind: 'query',
      query: `SELECT * FROM messages WHERE user_id = 'u_123' LIMIT 100`,
    },
  },
});

```

### Optimizations

### Segment size (batched lazy reads)

Read page 7 → fetch segment 5-8 (3 new pages)

 When a client references a page that is not stored locally, partial sync retrieves it from the remote database on demand. The **segment size** setting reduces round-trips for these lazy reads: rather than asking for only one page, the client can fetch an entire *segment* of pages per request. This spreads network overhead across more data and preloads nearby pages that may be read soon. **How it works** Suppose your database has:
- `page_size = 4 KiB`
- `segment_size = 16 KiB`
 When a local query reaches page **6**, the client identifies the segment containing that page:
- 16 KiB segment = 4 pages
- Segment covering page 6 = pages **5-8**
 The client downloads all four pages with one request and saves them locally. Later reads for those pages need no extra network request. **Benefits**
- Fewer HTTP requests (one segment fetch vs. many single-page fetches)
- Faster hydration of hot ranges
- Higher performance when access patterns show spatial locality, such as range scans or index lookups
 **Default** `segment_size` defaults to **128 KiB**. On a 4 KiB page size, that normally covers 32 pages, balancing fewer requests against extra transferred data. Workloads that read clustered data, such as several adjacent rows, may run much faster with larger segments. Sparse or random-access workloads may do better with smaller segment sizes.

```
await connect({
  ...
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
    segmentSize: 16 * 1024,
  },
});

```

### Prefetch

Read page 4 → prefetch 2 child pages (11, 12)

 Prefetch is an optional improvement layered on lazy page fetching. When active, the client retrieves the pages needed for the current query, then reviews the newly fetched page structure plus recent access history to estimate which pages may be useful next. If the access pattern appears to continue naturally, for example through child pages referenced by an internal B-tree node, the client downloads those pages early. This can lower the number of later on-demand fetches and reduce pauses during range scans, index walks, or sequential lookups.

```
await connect({
  ...
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
    prefetch: true,
  },
});

```

 `segment_size` and `prefetch` work well together. Segment size groups nearby pages into one on-demand fetch, while prefetch examines the query's access pattern and downloads additional likely-needed pages ahead of time. Combining them often gives the strongest performance on realistic workloads.

## Offline-first writes

This guide explains how to create a Turso database and use sync from an application. This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account. 1

1. Setup Turso Cloud database

- Use the [Quickstart](https://docs.turso.tech/quickstart) to install the CLI and create a database
-  Get the database URL (`libsql://...`):

```
turso db show <db>

```

- Generate a token for the app:

```
turso db tokens create <db>

```

2

2. Set up a basic sync connection

Sync setup requires three values:
- Local path: the storage location for the local synced tursodb file
- Remote URL: the Turso Cloud URL (`libsql://...`)
- Auth token: the Turso Cloud token used to authenticate requests

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',                               // local path
  url: 'libsql://...',                            // remote URL (generated with turso db show <db-name> --url)
  authToken: process.env.TURSO_AUTH_TOKEN,        // authentication token (generated with turso db tokens create <db-name>)
  // longPollTimeoutMs: 10_000,                   // optional: server waits before replying to pull
  // bootstrapIfEmpty: false,                     // set to false to avoid bootstrapping on first run
});

```

During the first run, the local database bootstraps itself from the remote, so the initial connection must be able to reach the remote. If `bootstrap_if_empty` is set to `false`, the local database begins empty instead. You can bootstrap it or refresh it later by calling `pull()` explicitly. 3

3. Push changes

Push transfers local changes to the Turso Cloud server. Internally, it sends logical statements, and conflicts use the "last push wins" strategy.

```
await db.exec("CREATE TABLE IF NOT EXISTS notes(id TEXT PRIMARY KEY, body TEXT)");
await db.exec("INSERT INTO notes VALUES ('n1', 'hello')");

await db.push();

```

4

4. Pull changes

Pull retrieves remote updates and applies them to the local database. Its return value is a boolean showing whether any change was applied.
- Set `long_poll_timeout_ms`/`LongPollTimeoutMs` when the server should wait for changes instead of returning empty responses immediately.
- After an earlier push, a later pull may still report a change because of conflict-resolution frames produced on the server.

```
// Returns true if anything changed locally
const changed = await db.pull();
console.info('pulled changes:', changed);

```

5

5. Checkpoint

Checkpoint reduces the local WAL so local disk usage stays bounded while sync state is retained.

```
await db.checkpoint();

```

6

6. Stats

Stats expose sync activity and resource use, including WAL sizes, latest push/pull times, network usage, revision, and similar values.

```
const s = await db.stats();
console.info({
  cdcOperations: s.cdcOperations,
  mainWalSize: s.mainWalSize,
  revertWalSize: s.revertWalSize,
  networkReceivedBytes: s.networkReceivedBytes,
  networkSentBytes: s.networkSentBytes,
  lastPullUnixTime: s.lastPullUnixTime,
  lastPushUnixTime: s.lastPushUnixTime,
  revision: s.revision,
});

```

### Offline-first writes

 For apps that must accept writes while offline, write to the local database first and call `push()` once connectivity returns. The local database file keeps every change until syncing is possible.

```
import { connect } from '@tursodatabase/sync';

// bootstrapIfEmpty: false lets the app start offline without
// needing to reach the remote on first launch
const db = await connect({
  path: './local.db',
  url: process.env.TURSO_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN!,
  bootstrapIfEmpty: false,
});

async function syncWhenOnline(db) {
  try {
    await db.push();
  } catch (e) {
    // No connectivity — changes are safe in the local file
    // and will sync on the next push() call
  }
}

// On app launch, pull the latest state if online
try {
  await db.pull();
} catch (e) {
  // Offline — local data is still available for reads and writes
}

// On a timer or connectivity event
await syncWhenOnline(db);

```

 This replaces the older `offline: true` option from [`@libsql/client` Embedded Replicas](https://docs.turso.tech/features/embedded-replicas/introduction). In Turso Sync, reads and writes are local by default, and syncing happens only when you explicitly call `push()` and `pull()`.
