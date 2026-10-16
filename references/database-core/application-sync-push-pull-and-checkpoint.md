# Application sync push pull and checkpoint

[Context index](database-sync-workflows.md) · [Setup and preceding context](application-sync-connection-and-bootstrap.md) · [Continue](application-sync-offline-writes.md)

3. Push changes

Push transfers local changes to the Turso Cloud server. Internally, it sends logical statements, and conflicts use the "last push wins" strategy.

```
await db.exec("CREATE TABLE IF NOT EXISTS notes(id TEXT PRIMARY KEY, body TEXT)");
await db.exec("INSERT INTO notes VALUES ('n1', 'hello')");

await db.push();

```

### Step 4: Pull changes

Pull retrieves remote updates and applies them to the local database. Its return value is a boolean showing whether any change was applied.
- Set `long_poll_timeout_ms`/`LongPollTimeoutMs` when the server should wait for changes instead of returning empty responses immediately.
- After an earlier push, a later pull may still report a change because of conflict-resolution frames produced on the server.

```
// Returns true if anything changed locally
const changed = await db.pull();
console.info('pulled changes:', changed);

```

### Step 5: Checkpoint

Checkpoint reduces the local WAL so local disk usage stays bounded while sync state is retained.

```
await db.checkpoint();

```

### Step 6: Stats

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
