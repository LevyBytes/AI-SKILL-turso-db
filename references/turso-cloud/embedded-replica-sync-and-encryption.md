# Embedded replica sync and encryption

[Context index](replica-hosting-and-recovery-workflows.md) · [Continue](embedded-replica-usage-and-deployment-options.md)

## How it works

Embedded Replicas maintain a local read replica for a Turso Cloud database. Reads come from the local file in microseconds, while writes go to the cloud primary before being propagated back to the replica. Embedded Replicas are production-supported. For new projects that require sync, Turso recommends [Turso Sync](https://docs.turso.tech/sync/usage). Sync runs on the Turso Database engine, uses logical change-data-capture, supports local-first writes and explicit `push()` / `pull()`, and uses [significantly less bandwidth and lower latency](https://turso.tech/blog/sync-benchmark) than the page-level replication used by Embedded Replicas. With Turso Cloud's embedded replicas, an application can replicate a database inside its own runtime. This helps VM or VPS deployments and mobile apps that cannot rely on stable connectivity, because the local database remains available without interruption. Embedded replicas let the same database move between local and remote operation patterns, and by syncing local copies from the remote database they provide microsecond-level read access.

### How it works

1.  Set a local file as the main database.
  - Configure that file through the client `url` parameter.

2.  Set the remote database that the client should sync with.
  - Use the client configuration parameter `syncUrl` for that remote database.

3.  You read from a database:
  - All reads come from the local replica identified by `url`.

4.  You write to a database:
  - By default, **writes go to the remote primary database** specified by `syncUrl`; they are NOT first persisted to the local file.
  - To write locally, set the `offline` configuration option to `true`.
  - Write transactions that include reads are sent to the remote primary database as well.
  - After a write succeeds, the local database receives those changes automatically; read your own writes is enabled unless disabled.

### Periodic sync

 Automatic syncing to an embedded replica is available through the periodic sync interval property. Provide `syncInterval` when creating the client:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  authToken: "...",
  syncUrl: "...",
  syncInterval: 60,
  offline: true, // Optional: Enable offline mode (default: false)
});

```

### Read your writes

 Embedded Replicas also provide read-your-writes semantics. After a successful write, the replica that made that write can immediately read the new data even without calling `sync()`. Other replicas receive the data when they call `sync()`, or on the next interval when Periodic Sync is enabled. (Figure: Read your writes)

### Encryption at rest

 Encryption at rest is supported for Embedded Replicas when using one of the libSQL client SDKs. Supply `encryptionKey` as you instantiate the client:

```
import { createClient } from "@libsql/client";

const db = createClient({
  url: "file:encrypted.db",
  encryptionKey: process.env.ENCRYPTION_KEY,
});

```

 You are responsible for generating and managing the encryption key.
