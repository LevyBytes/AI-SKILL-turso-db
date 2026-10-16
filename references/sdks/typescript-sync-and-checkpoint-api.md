# TypeScript sync and checkpoint API

[Context index](typescript-package-and-api-selection.md)

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
