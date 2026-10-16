# Rust Turso sync and checkpoint API

[Context index](rust-package-and-api-selection.md) · [Setup and preceding context](rust-turso-statements-transactions-and-encryption.md)

### Sync (Push and Pull)

 Use this option for a local database that syncs with the cloud. Reads and writes happen locally; call `push()` to send local changes and `pull()` to fetch remote updates. Enable the `sync` feature:

```
cargo add turso --features sync

```

```
use turso::sync::Builder;

let db = Builder::new_remote("app.db")
    .with_remote_url(&std::env::var("TURSO_DATABASE_URL")?)
    .with_auth_token(&std::env::var("TURSO_AUTH_TOKEN")?)
    .bootstrap_if_empty(true)  // Download schema on first sync (default)
    .build()
    .await?;

let conn = db.connect().await?;

```

 On the first run, the local database is created automatically from the remote. See [Turso Sync](https://docs.turso.tech/sync/usage) for the complete workflow.

#### Push and Pull

```
// Push local writes to Turso Cloud
db.push().await?;

// Pull remote changes (returns true if changes were applied)
let changed = db.pull().await?;

```

#### Checkpoint

 Keep disk use bounded without losing sync state by shrinking the local WAL:

```
db.checkpoint().await?;

```

#### Stats

```
let stats = db.stats().await?;
println!("Network received: {} bytes", stats.network_received_bytes);
println!("Network sent: {} bytes", stats.network_sent_bytes);
println!("WAL size: {} bytes", stats.main_wal_size);

```
