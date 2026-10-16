# Rust Turso cloud sync quickstart

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](rust-turso-local-quickstart.md)

### Step 3: Sync (push and pull)

Enable the `sync` feature when the local database needs to synchronize with Turso Cloud:

```
cargo add turso --features sync

```

```
use turso::sync::Builder;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let db = Builder::new_remote("app.db")
        .with_remote_url(&std::env::var("TURSO_DATABASE_URL")?)
        .with_auth_token(&std::env::var("TURSO_AUTH_TOKEN")?)
        .build()
        .await?;

    let conn = db.connect().await?;

    conn.execute("INSERT INTO users (name) VALUES (?)", ("Bob",)).await?;

    // Push local writes to Turso Cloud
    db.push().await?;

    // Pull remote changes to local database
    db.pull().await?;

    Ok(())
}

```

Reads and writes use the local database file, which keeps access fast and available offline. Call `push()` to upload local changes, and call `pull()` to fetch remote changes. The [reference](https://docs.turso.tech/sdk/rust/reference) covers checkpoint, stats, and encryption. [Turso Sync](https://docs.turso.tech/sync/usage) explains conflict handling and related details.Start a local sync server when you want to test synchronization without a Turso Cloud account:

```
tursodb :memory: --sync-server 127.0.0.1:8080

```

Use `http://127.0.0.1:8080` as the remote URL; no auth token is required. See [Turso Database quickstart](https://docs.turso.tech/tursodb/quickstart) for installing `tursodb`.
