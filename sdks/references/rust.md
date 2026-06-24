# Examples, Guides, Orm, Quickstart

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: examples; Prerequisites; Recommended: turso (Local + Cloud Sync).

## examples

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [libsql](https://docs.turso.tech/tursodatabase/libsql) ** Public
-  [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql) Sign in before changing notification settings
-  [Fork 500](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql)
-   [Star  16.9k](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql)

### Expand file tree

/

### examples

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/libsql/commits/main/libsql/examples)History/

### examples

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| deserialization.rs | deserialization.rs |  |  |
| encryption_local.rs | encryption_local.rs |  |  |
| encryption_remote.rs | encryption_remote.rs |  |  |
| encryption_sync.rs | encryption_sync.rs |  |  |
| example.rs | example.rs |  |  |
| example_v2.rs | example_v2.rs |  |  |
| flutter.rs | flutter.rs |  |  |
| local_sync.rs | local_sync.rs |  |  |
| offline_writes.rs | offline_writes.rs |  |  |
| remote_sync.rs | remote_sync.rs |  |  |
| replica.rs | replica.rs |  |  |
| transaction.rs | transaction.rs |  |  |

## Prerequisites

(Figure: Actix banner)

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Actix app - [learn more](https://actix.rs/docs/getting-started)
 1

Retrieve database credentials

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

Store these values as environment variables.2

Add the libsql crate to the project

```
cargo add libsql

```

Optionally add a package such as [`dotenvy`](https://docs.rs/dotenvy/latest/dotenvy) to make `.env` files easier to use:

```
cargo add dotenvy

```

3

Execute SQL

```
#[tokio::main]
#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| App::new().route("/", web::get().to(index)).route("/items", web::get().to(get_items)))
        .bind(("127.0.0.1", 8080))?
        .run()
        .await
}

async fn get_items() -> Result<HttpResponse, Error> {
    dotenv().expect(".env file not found");

    let db_url = env::var("TURSO_DATABASE_URL").unwrap();
    let auth_token = env::var("TURSO_AUTH_TOKEN").unwrap();
    let db_file = env::var("LOCAL_DB").unwrap();

    let db = Builder::new_remote_replica(db_file, url, auth_token)
    .read_your_writes(true)
    .build()
    .await
    .unwrap();

    let conn = db.connect().unwrap();

    let mut results = conn.query("SELECT * FROM items", ()).await.unwrap();

    let mut items: Vec<T> = Vec::new();

    while let Some(row) = results.next().await.unwrap() {
        let item: Item = Item {
            task: row.get(0).unwrap(),
        };
        items.push(item);
    }

    Ok(HttpResponse::Ok().json(items))
}

```

### Examples

### Turso + Actix Web Traffic Tracker

See the full source code

## Prerequisites

(Figure: Axum banner)

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Axum app - [learn more](https://github.com/tokio-rs/axum)
 1

Retrieve database credentials

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

Store these values as environment variables.2

Add the libsql crate to the project

```
cargo add libsql

```

Optionally add a package such as [`dotenvy`](https://docs.rs/dotenvy/latest/dotenvy) to make `.env` files easier to use:

```
cargo add dotenvy

```

3

Execute SQL

```
use libsql::{Builder, Connection, Database, Result};

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

4

Use in an Axum handler

```
use axum::{
    extract::State,
    routing::get,
    Json, Router,
};
use libsql::{Builder, Connection, Database};
use serde::Serialize;

#[derive(Clone)]
struct AppState {
    db: Database,
}

#[derive(Serialize)]
struct User {
    id: i64,
    name: String,
}

async fn get_users(State(state): State<AppState>) -> Json<Vec<User>> {
    let conn = state.db.connect().unwrap();
    let mut rows = conn.query("SELECT id, name FROM users", ()).await.unwrap();

    let mut users = Vec::new();
    while let Some(row) = rows.next().await.unwrap() {
        users.push(User {
            id: row.get(0).unwrap(),
            name: row.get(1).unwrap(),
        });
    }

    Json(users)
}

#[tokio::main]
async fn main() {
    let db_url = std::env::var("TURSO_DATABASE_URL").expect("TURSO_DATABASE_URL must be set");
    let auth_token = std::env::var("TURSO_AUTH_TOKEN").expect("TURSO_AUTH_TOKEN must be set");

    let db = Builder::new_remote(db_url, auth_token)
        .build()
        .await
        .unwrap();

    let app_state = AppState { db };

    let app = Router::new()
        .route("/users", get(get_users))
        .with_state(app_state);

    axum::Server::bind(&"0.0.0.0:3000".parse().unwrap())
        .serve(app.into_make_service())
        .await
        .unwrap();
}

```

This example puts a shared `Database` instance in application state and uses it from the handler to run queries.

## Prerequisites

(Figure: Rocket banner)

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Rocket app - [learn more](https://rocket.rs/v0.5/guide/getting-started/)
 1

Retrieve database credentials

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

Store these values as environment variables.2

Add the libsql crate to the project

```
cargo add libsql

```

Optionally add a package such as [`dotenvy`](https://docs.rs/dotenvy/latest/dotenvy) to make `.env` files easier to use:

```
cargo add dotenvy

```

3

Execute SQL

```
#[get("/todos")]
async fn get_todos() -> Json<Vec<Todo>> {
    dotenv().expect(".env file not found");

    let url = env::var("TURSO_DATABASE_URL").expect("TURSO_DATABASE_URL not found!");
    let token = env::var("TURSO_AUTH_TOKEN").expect("TURSO_AUTH_TOKEN not found!");

    let db = Database::open_remote(url, token).unwrap();
    let conn = db.connect().unwrap();

    let mut response = conn.query("select * from todos", ()).await.unwrap();

    let mut todos: Vec<Todo> = Vec::new();
    while let Some(row) = response.next().unwrap() {
        let todo: Todo = Todo {
            task: row.get(0).unwrap(),
        };
        todos.push(todo);
    }

    Json(todos)
}

#[launch]
fn rocket() -> _ {
    dotenv().ok();
    rocket::build().mount("/", routes![get_todos])
}

```

### Examples

### Turso + Rocket Todo List

See the full source code

## Prerequisites

(Figure: Tauri banner)

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Tauri app - [learn more](https://tauri.app/v1/guides/getting-started/setup)
 1

Add these crates to the Tauri project's dependencies

src-tauri/Cargo.toml

```
[dependencies]
serde_json = "1.0"
serde = { version = "1.0", features = ["derive"] }
reqwest = { version = "0.11.22", features = ["json", "blocking"] }
dotenvy = "0.15.7"
tokio = { version = "1", features = ["full"] }
libsql = { git = "https://github.com/tursodatabase/libsql" }
tracing-subscriber = "0.3"
tracing = "0.1.40"

[patch.crates-io]
sqlite3-parser = { git = "https://github.com/LucioFranco/lemon-rs" }

```

2

Fetch Rust dependencies from inside the `src-tauri` directory.

```
cargo fetch

```

3

Configure database credentials

Get the database URL.

```
turso db show --url <database-name>

```

Get the database authentication token.

```
turso db tokens create <database-name>

```

Put the acquired credentials into environment variables in `src-tauri/.env`.src-tauri/.env

```
TURSO_SYNC_URL="..."
TURSO_AUTH_TOKEN="..."
DB_PATH=./my-data.db # embedded replica

```

4

Execute SQL

src-tauri/src/main.rs

```
use dotenvy::dotenv;
use libsql::{params, Database};
use serde::{Deserialize, Serialize};
use std::env;

use tracing::info;

#[derive(Serialize, Debug)]
struct Error {
    msg: String,
}

type Result<T> = std::result::Result<T, Error>;

impl<T> From<T> for Error
where
    T: std::error::Error,
{
    fn from(value: T) -> Self {
        Self {
            msg: value.to_string(),
        }
    }
}

#[derive(Deserialize, Serialize, Debug)]
pub struct Item {
    id: String,
    foo: String,
    bar: String,
}

#[tauri::command]
async fn get_all_items() -> Result<Vec<Item>> {
    dotenv().expect(".env file not found");

    let db_path = env::var("DB_PATH").unwrap();
    let sync_url = env::var("TURSO_SYNC_URL").unwrap();
    let auth_token = env::var("TURSO_AUTH_TOKEN").unwrap();

    let db = Database::open_with_remote_sync(db_path, sync_url, auth_token).await?;

    let conn = db.connect()?;

    let mut results = conn
        .query("SELECT * FROM table_name", ())
        .await?;

    let mut items: Vec<Item> = Vec::new();
    while let Some(row) = results.next()? {
        let note: Item = Item {
            id: row.get(0)?,
            foo: row.get(1)?,
            bar: row.get(2)?,
        };
        items.push(item);
    }

    Ok(items)
}

fn main() {
    tracing_subscriber::fmt::init();

    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![
            get_all_items,
        ])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}

```

### Examples

### Personal Notes App

See the full source code

## Prerequisites

[Toasty](https://github.com/tokio-rs/toasty) is a Rust async ORM maintained by the [Tokio](https://tokio.rs/) project. Its native Turso driver (`toasty-driver-turso`) connects to a local Turso Database file or to an in-memory database, using the same engine exposed by the [`turso` crate](https://docs.turso.tech/sdk/rust/quickstart). The Toasty Turso driver currently supports local and in-memory databases. For Turso Cloud sync, use the [`turso` crate directly](https://docs.turso.tech/sdk/rust/quickstart#sync-push-and-pull).

### Prerequisites

- Rust 1.95 or newer, which is Toasty's minimum supported version
- A Rust project (`cargo init`)
- Tokio as your async runtime
 1

Install Toasty with the Turso driver

```
cargo add toasty --features turso
cargo add tokio --features full
cargo add uuid

```

2

Define your models

Toasty infers schema, queries, and relations from ordinary Rust structs:models.rs

```
#[derive(Debug, toasty::Model)]
struct User {
    #[key]
    #[auto]
    id: uuid::Uuid,

    name: String,

    #[unique]
    email: String,

    #[has_many]
    todos: toasty::Deferred<Vec<Todo>>,
}

#[derive(Debug, toasty::Model)]
struct Todo {
    #[key]
    #[auto]
    id: uuid::Uuid,

    #[index]
    user_id: uuid::Uuid,

    #[belongs_to(key = user_id, references = id)]
    user: toasty::Deferred<User>,

    title: String,
}

```

3

Connect to Turso

Give `Db::builder` a `turso:` URL, either for an in-memory database or a file path:

In-memory

```
let db = toasty::Db::builder()
    .models(toasty::models!(crate::*))
    .connect("turso::memory:")
    .await?;

```

File-backed

```
let db = toasty::Db::builder()
    .models(toasty::models!(crate::*))
    .connect("turso:./app.db")
    .await?;

```

With concurrent writes (MVCC)

To turn on Turso's MVCC journal for concurrent writers, explicitly build the driver and pass it into `build()`:

```
use toasty_driver_turso::Turso;

let driver = Turso::file("app.db").concurrent_writes();

let db = toasty::Db::builder()
    .models(toasty::models!(crate::*))
    .build(driver)
    .await?;

```

With `concurrent_writes()` enabled, write-write conflicts appear as serialization failures; test `err.is_serialization_failure()` and retry the transaction.4

Create the schema and query

```
#[tokio::main]
async fn main() -> toasty::Result<()> {
    let mut db = toasty::Db::builder()
        .models(toasty::models!(crate::*))
        .connect("turso:./app.db")
        .await?;

    db.push_schema().await?;

    let user = toasty::create!(User {
        name: "Ada Lovelace",
        email: "ada@example.com",
    })
    .exec(&mut db)
    .await?;

    toasty::create!(in user.todos() { title: "ship Toasty + Turso" })
        .exec(&mut db)
        .await?;

    let reloaded = User::get_by_email(&mut db, &user.email).await?;
    println!("{reloaded:#?}");

    Ok(())
}

```

### Where to go next

- The [Toasty guide](https://tokio-rs.github.io/toasty/) explains models, relations, transactions, and migrations.
- The [Turso chapter](https://tokio-rs.github.io/toasty/nightly/guide/turso.html) in the Toasty guide covers connection URL forms, the `concurrent_writes()` flag, and `experimental_*` toggles for Turso engine features.
- The [`turso` crate quickstart](https://docs.turso.tech/sdk/rust/quickstart) demonstrates direct Turso Database usage without an ORM, including sync with Turso Cloud.

## Recommended: turso (Local + Cloud Sync)

This Rust quickstart covers how to:
- Install the Turso crate
- Connect to a local or remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: turso (Local + Cloud Sync)

 Use `turso` for local databases that also need Turso Cloud synchronization. The crate runs on the Turso Database engine: a SQLite-compatible implementation designed for MVCC concurrent writers, native async Rust APIs, and async I/O. 1

Install

```
cargo add turso tokio --features tokio/full

```

2

Connect

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

3

Sync (push and pull)

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

### Remote Access (Over-the-Wire)

 When an app must issue network queries directly to Turso Cloud, for example from server code or a serverless runtime, choose the `libsql` crate and enable the `remote` feature. This path speaks HTTP, avoids a local file, and does not require a C compiler. For most apps, local sync through `turso::sync` is the better default because reads are quicker, offline operation is possible, and latency is lower. Remote mode is appropriate when the runtime cannot keep a database file, as with stateless serverless deployments. 1

Retrieve database credentials

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

Store these values as environment variables.2

Install

```
cargo add libsql --features remote

```

3

Connect and query

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
