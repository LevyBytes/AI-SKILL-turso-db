# Embedded replica usage and deployment options

[Context index](replica-hosting-and-recovery-workflows.md) · [Setup and preceding context](embedded-replica-sync-and-encryption.md)

### Usage

 To use embedded replicas, create a client that includes `syncUrl`. That parameter gives the remote Turso Cloud database URL the client will sync against:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:replica.db",
  syncUrl: "libsql://...",
  authToken: "...",
});

```

 Remote changes can be synced to the local replica manually:

```
await client.sync();

```

 Call `.sync()` in the background at the points where the application should align the remote database and local embedded replica. Common examples are every 5 minutes or whenever the application starts.

### Things to know

- While an embedded replica is syncing, do not open the local database, because that can corrupt data.
- Embedded replicas cannot be used in some contexts, including serverless environments that do not provide a filesystem.
- Some scenarios can cause more frames to sync than expected.
  - If a write makes the internal btree split at any node, many new frames would be added to the replication log.
  - If a server restart leaves the on-disk wal dirty, the replication log would be regenerated and extra frames would sync.
  - Deleting or invalidating the on-disk local files could force the embedded replica to sync again from the beginning.

- A frame is 4kB of data, meaning one on disk page frame; writing a 1 byte row still appears as a 4kB write because libsql writes in that unit.

### Deployment Guides

### [Turso + Fly](https://docs.turso.tech/features/embedded-replicas/with-fly)

Deploy a JavaScript project that uses Embedded Replicas on Fly.io

### [Turso + Koyeb](https://docs.turso.tech/features/embedded-replicas/with-koyeb)

Deploy a JavaScript/Rust project using Embedded Replicas on Koyeb

### [Turso + Railway](https://docs.turso.tech/features/embedded-replicas/with-railway)

Deploy a JavaScript/Rust project using Embedded Replicas on Railway

### [Turso + Render](https://docs.turso.tech/features/embedded-replicas/with-render)

Deploy a JavaScript project using Embedded Replicas on Render

### [Turso + Linode by Akamai](https://docs.turso.tech/features/embedded-replicas/with-akamai)

Deploy a JavaScript/Rust project using Embedded Replicas on Akamai
