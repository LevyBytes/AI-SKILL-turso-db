# Rust remote access and ORM selection

[Context index](rust-framework-and-connection-workflows.md)

### Remote Access (Over-the-Wire)

 When an app must issue network queries directly to Turso Cloud, for example from server code or a serverless runtime, choose the `libsql` crate and enable the `remote` feature. This path speaks HTTP, avoids a local file, and does not require a C compiler. For most apps, local sync through `turso::sync` is the better default because reads are quicker, offline operation is possible, and latency is lower. Remote mode is appropriate when the runtime cannot keep a database file, as with stateless serverless deployments.

### Step 1: Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart).Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values as environment variables.

### Step 2: Install

```
cargo add libsql --features remote

```

### Step 3: Connect and query

```
use libsql::Builder;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let url = std::env::var("TURSO_DATABASE_URL")?;
    let token = std::env::var("TURSO_AUTH_TOKEN")?;

    let db = Builder::new_remote(url, token).build().await?;
    let conn = db.connect()?;

    let mut rows = conn.query("SELECT * FROM users", ()).await?;
    while let Some(row) = rows.next().await? {
        let id: i64 = row.get(0)?;
        let name: String = row.get(1)?;
        println!("User: {} {}", id, name);
    }

    Ok(())
}

```

### Using an ORM

 [Toasty](https://docs.turso.tech/sdk/rust/orm/toasty), the Tokio project's async ORM, includes a native Turso driver. It talks directly to the `turso` crate, providing a typed model layer (`#[derive(toasty::Model)]`, derived queries, and relations) over the same Turso Database engine used earlier in the quickstart.

```
cargo add toasty --features turso

```

 See the [Toasty + Turso guide](https://docs.turso.tech/sdk/rust/orm/toasty) for the complete walkthrough.

### Embedded Replicas (libsql)

 Embedded Replicas provide a local read copy of a Turso Cloud database for a Rust application. Reads use the local replica; writes go to the cloud primary and are then reflected back. Embedded Replicas are production-supported.For new sync-focused projects, prefer the `turso` crate with `turso::sync`: reads and writes stay local, synchronization is explicit through `push()` / `pull()`, and the wire format uses logical change-data-capture instead of page frames ([benchmark](https://turso.tech/blog/sync-benchmark)). See the [reference](https://docs.turso.tech/sdk/rust/reference) for full Embedded Replicas documentation with `libsql`.
