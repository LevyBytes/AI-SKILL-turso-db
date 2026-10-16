# Rust Turso statements transactions and encryption

[Context index](rust-package-and-api-selection.md) · [Continue](rust-turso-sync-and-checkpoint-api.md)

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
