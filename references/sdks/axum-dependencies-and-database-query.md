# Axum dependencies and database query

[Context index](rust-framework-and-connection-workflows.md) · [Continue](axum-shared-database-state-and-handler.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Axum app - [learn more](https://github.com/tokio-rs/axum)
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

### Step 2: Add the libsql crate to the project

```
cargo add libsql

```

Optionally add a package such as [`dotenvy`](https://docs.rs/dotenvy/latest/dotenvy) to make `.env` files easier to use:

```
cargo add dotenvy

```

### Step 3: Execute SQL

```
use dotenvy::dotenv;
use libsql::{Builder, Result};

#[tokio::main]
async fn main() -> Result<()> {
    dotenv().ok();

    let db_url = std::env::var("TURSO_DATABASE_URL").expect("TURSO_DATABASE_URL must be set");
    let auth_token = std::env::var("TURSO_AUTH_TOKEN").expect("TURSO_AUTH_TOKEN must be set");

    let db = Builder::new_remote(db_url, auth_token)
        .build()
        .await?;

    let conn = db.connect()?;

    // Execute a query
    let mut rows = conn.query("SELECT * FROM users", ()).await?;

    while let Some(row) = rows.next().await? {
        let id: i64 = row.get(0)?;
        let name: String = row.get(1)?;
        println!("User: {} - {}", id, name);
    }

    Ok(())
}

```
