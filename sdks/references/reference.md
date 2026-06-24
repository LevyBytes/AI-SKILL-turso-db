# Rust — Reference

> Sdks reference. Original prose; identifiers preserved verbatim.

Turso offers two Rust crates:
|  | `turso` | `libsql` |
| --- | --- | --- |
| **Use case** | Local / embedded database, sync | Remote access, existing libSQL codebases |
| **Engine** | Turso Database (rewrite) | libSQL (SQLite fork) |
| **Concurrent writes** | Yes (MVCC) | Not supported |
| **Sync** | push/pull (local-first) | Embedded Replicas, where writes are forwarded to the cloud primary |
| **C compiler** | Not required | Required for `core`, `replication`, `encryption` features |

 **Starting a new project?** Use `turso` for local or embedded storage and sync. Use `libsql` with the `remote` feature for over-the-wire access that does not need a C compiler.

### turso

Built for local and embedded use cases. The underlying Turso Database engine provides MVCC-based concurrent writers and async I/O.

### Installing

```
cargo add turso tokio --features tokio/full

```

### Connecting

```
use turso::Builder;

let db = Builder::new_local("app.db").build().await?;
let conn = db.connect()?;

```

 In-memory databases are also supported:

```
let db = Builder::new_local(":memory:").build().await?;

```

### Querying

```
conn.execute(
    "CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL
    )",
    (),
).await?;

conn.execute("INSERT INTO users (name) VALUES (?)", ("Alice",)).await?;

let mut rows = conn.query("SELECT * FROM users", ()).await?;
while let Some(row) = rows.next().await? {
    let id: i64 = row.get(0)?;
    let name: String = row.get(1)?;
    println!("User: {} {}", id, name);
}

```

### Prepared Statements

```
let mut stmt = conn.prepare("SELECT * FROM users WHERE id = ?1").await?;
let mut rows = stmt.query([42]).await?;

```

### Transactions

```
let tx = conn.transaction().await?;

tx.execute("INSERT INTO users (name) VALUES (?1)", ["Alice"]).await?;
tx.execute("INSERT INTO users (name) VALUES (?1)", ["Bob"]).await?;

tx.commit().await?;

```

### Encryption

 Encrypt local databases at rest:

```
use turso::{Builder, EncryptionOpts};

let db = Builder::new_local("encrypted.db")
    .experimental_encryption(true)
    .with_encryption(EncryptionOpts {
        cipher: "aegis256".to_string(),
        hexkey: "b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327".to_string(),
    })
    .build()
    .await?;

```

Available cipher choices are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, and `aes128gcm`. Encrypted databases cannot be read as standard SQLite databases; open them through the Turso Database engine. Turso Cloud also supports bring-your-own-key encryption; see [learn more](https://docs.turso.tech/cloud/encryption).

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

### libsql (Remote)

 For over-the-wire Turso Cloud access, use the `libsql` crate with the `remote` feature. It is implemented as pure Rust HTTP, so no C compiler is needed.

### Installing

```
cargo add libsql --features remote

```

### Connecting

```
use libsql::Builder;

let url = std::env::var("TURSO_DATABASE_URL")?;
let token = std::env::var("TURSO_AUTH_TOKEN")?;

let db = Builder::new_remote(url, token).build().await?;
let conn = db.connect()?;

```

### Querying

```
let mut rows = conn.query("SELECT * FROM users WHERE id = ?1", [1]).await?;

```

### libsql (libSQL)

 The `libsql` crate is based on [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork that currently powers Turso Cloud. It is production-ready, proven in real systems, and best suited to codebases that already rely on `libsql`. With `libsql` Embedded Replicas, reads stay local while writes go to the cloud primary and are later reflected back on the replica. Embedded Replicas are fully supported. For new sync-oriented projects, prefer `turso` with `turso::sync`; see the [quickstart](https://docs.turso.tech/sdk/rust/quickstart).

### Embedded Replicas

 [Embedded Replicas](https://docs.turso.tech/features/embedded-replicas) can sync a Turso Cloud database into a local SQLite file. Reads run locally, while writes are sent to the cloud primary and then mirrored back to the replica:

```
use libsql::Builder;

let url = std::env::var("TURSO_DATABASE_URL")?;
let token = std::env::var("TURSO_AUTH_TOKEN")?;

let db = Builder::new_remote_replica("local.db", url, token)
    .build()
    .await?;
let conn = db.connect()?;

```

 Embedded Replicas need an environment with filesystem access.

#### Manual Sync

```
db.sync().await?;

```

#### Sync Interval

```
use std::time::Duration;

let db = Builder::new_remote_replica("local.db", url, token)
    .sync_interval(Duration::from_secs(300))
    .build()
    .await?;

```

#### Read Your Own Writes

 By default, after `push()` finishes, the following `pull()` waits until the server has processed those changes so read-your-own-writes behavior is preserved. That safer behavior is **much slower** because the server must catch up first. Disable it for faster pulls when eventually consistent reads are acceptable:

```
let db = Builder::new_remote_replica("local.db", url, token)
    .read_your_writes(false)
    .build()
    .await?;

```

### Encryption

 For new projects requiring local encryption, use the `turso` crate; it uses the Turso Database engine and offers stronger performance with concurrent writes. To encrypt a SQLite file, pass the encryption key value into the constructor: Rust

```
use libsql::Builder;
use bytes::Bytes;

let url = env::var("LIBSQL_URL").expect("LIBSQL_URL must be set");
let token = env::var("LIBSQL_AUTH_TOKEN").unwrap_or_default();

let cipher = Cipher::YourChosenCipher;
let encryption_key_bytes = Bytes::from("your_secure_encryption_key_here");

let encryption_config = EncryptionConfig {
    cipher,
    encryption_key: encryption_key_bytes,
};

let mut db = Builder::new_remote_replica("local.db", &url, &token)
  .encryption_config(encryption_config) // Apply encryption configuration
  .build()
  .await
  .unwrap();

let conn = db.connect().unwrap();

```

 Encrypted databases appear as raw bytes and cannot be opened as normal SQLite databases. Use the libSQL client for every operation; see [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

### Conditional compilation

 The `libsql` crate supports Cargo [conditionally compiling](https://doc.rust-lang.org/cargo/reference/features.html#dependency-features) features:
| Feature | Description |
| --- | --- |
| `remote` | Pure Rust HTTP-only client. No C compiler is required. |
| `core` | Local databases only. Requires a C compiler. |
| `replication` | Combines `core` with embedded replica support. Requires a C compiler. |
| `encryption` | Encryption at rest. Requires `cmake` and is disabled by default. |

### Simple query

```
conn.execute("SELECT * FROM users", ()).await?;
conn.execute("SELECT * FROM users WHERE id = ?1", [1]).await?;

```

### Placeholders

```
conn.execute("SELECT * FROM users WHERE id = ?1", libsql::params![1]).await?;

```

### Deserialization

```
use libsql::{de, Builder};

#[derive(Debug, serde::Deserialize)]
struct User {
    name: String,
    age: i64,
}

let mut stmt = conn.prepare("SELECT * FROM users WHERE id = ?1").await?;
let row = stmt.query([1]).await?.next().await?.unwrap();
let user = de::from_row::<User>(&row)?;

```

### Batch Transactions

```
conn.execute_batch(r#"
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
  );

  INSERT INTO users (name) VALUES ('Alice');
  INSERT INTO users (name) VALUES ('Bob');
"#).await?;

```

### Interactive Transactions

```
let tx = conn.transaction().await?;

tx.execute("INSERT INTO users (name) VALUES (?1)", ["Alice"]).await?;
tx.execute("INSERT INTO users (name) VALUES (?1)", ["Bob"]).await?;

tx.commit().await?;

```
