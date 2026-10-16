# Rust Turso local quickstart

[Context index](rust-framework-and-connection-workflows.md) · [Continue](rust-turso-cloud-sync-quickstart.md)

## Recommended: turso (Local + Cloud Sync)

This Rust quickstart covers how to:
- Install the Turso crate
- Connect to a local or remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: turso (Local + Cloud Sync)

 Use `turso` for local databases that also need Turso Cloud synchronization. The crate runs on the Turso Database engine: a SQLite-compatible implementation designed for MVCC concurrent writers, native async Rust APIs, and async I/O.

### Step 1: Install

```
cargo add turso tokio --features tokio/full

```

### Step 2: Connect

```
use turso::Builder;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let db = Builder::new_local("app.db").build().await?;
    let conn = db.connect()?;

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

    Ok(())
}

```
