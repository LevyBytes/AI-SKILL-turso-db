# Application sync offline writes

[Context index](database-sync-workflows.md) · [Setup and preceding context](application-sync-connection-and-bootstrap.md) · [Previous phase](application-sync-push-pull-and-checkpoint.md)

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
