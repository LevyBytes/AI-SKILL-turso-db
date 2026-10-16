# Sync checkpoints and wal storage

[Context index](database-sync-workflows.md)

Included topics: Overview; Last Push Wins; Starting the server; Modes; Offline-first writes.

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
