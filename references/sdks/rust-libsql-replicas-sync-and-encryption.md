# Rust libSQL replicas sync and encryption

[Context index](rust-package-and-api-selection.md) · [Continue](rust-libsql-queries-and-transactions.md)

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

 `read_your_writes(true)` makes successful remote writes visible in the local replica before returning. Disable it when later reads may tolerate stale replica data; this setting belongs to libSQL embedded replicas, not the separate Turso push/pull API.

```
let db = Builder::new_remote_replica("local.db", url, token)
    .read_your_writes(false)
    .build()
    .await?;

```

### Encryption

 For new projects requiring local encryption, use the `turso` crate; it uses the Turso Database engine and offers stronger performance with concurrent writes. For this libSQL 0.9.30 example, enable its `encryption` Cargo feature and run the snippet inside a fallible async function. Supply a secret `LIBSQL_ENCRYPTION_KEY`; its AES-256-CBC contract is distinct from the Turso engine's AEAD ciphers.

```rust
use libsql::{Builder, Cipher, EncryptionConfig};

let url = std::env::var("TURSO_DATABASE_URL")?;
let token = std::env::var("TURSO_AUTH_TOKEN")?;
let key = std::env::var("LIBSQL_ENCRYPTION_KEY")?;
let encryption_config = EncryptionConfig {
    cipher: Cipher::Aes256Cbc,
    encryption_key: key.into(),
};
let db = Builder::new_remote_replica("local.db", url, token)
    .encryption_config(encryption_config)
    .build()
    .await?;
let conn = db.connect()?;
```

 Encrypted databases appear as raw bytes and cannot be opened as normal SQLite databases. Use the libSQL client for every operation; see [learn more](https://docs.turso.tech/libsql#encryption-at-rest).
