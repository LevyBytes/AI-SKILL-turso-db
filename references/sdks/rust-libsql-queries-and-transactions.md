# Rust libSQL queries and transactions

[Context index](rust-package-and-api-selection.md) · [Setup and preceding context](rust-libsql-replicas-sync-and-encryption.md)

### Conditional compilation

 The `libsql` crate supports Cargo [conditionally compiling](https://doc.rust-lang.org/cargo/reference/features.html#dependency-features) features:

| Feature | Description |
| --- | --- |
| `remote` | Pure Rust HTTP-only client. No C compiler is required. |
| `core` | Local databases only. Requires a C compiler. |
| `replication` | Combines `core` with embedded replica support. Requires a C compiler. |
| `encryption` | Encryption at rest. Requires `cmake` and is disabled by default. |

### Simple query

Use the `conn` from the connection reference. `query` returns `Rows`; consume rows with `rows.next().await?` when the result is needed.

```
conn.query("SELECT * FROM users", ()).await?;
conn.query("SELECT * FROM users WHERE id = ?1", [1]).await?;

```

### Placeholders

```
conn.query("SELECT * FROM users WHERE id = ?1", libsql::params![1]).await?;

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
