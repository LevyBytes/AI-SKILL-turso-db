# Toasty models and Turso dependencies

[Context index](rust-framework-and-connection-workflows.md) · [Continue](toasty-connections-schema-and-queries.md)

## Prerequisites

[Toasty](https://github.com/tokio-rs/toasty) is a Rust async ORM maintained by the [Tokio](https://tokio.rs/) project. Its native Turso driver (`toasty-driver-turso`) connects to a local Turso Database file or to an in-memory database, using the same engine exposed by the [`turso` crate](https://docs.turso.tech/sdk/rust/quickstart). The Toasty Turso driver currently supports local and in-memory databases. For Turso Cloud sync, use the [`turso` crate directly](https://docs.turso.tech/sdk/rust/quickstart#sync-push-and-pull).

### Prerequisites

- Rust 1.95 or newer, which is Toasty's minimum supported version
- A Rust project (`cargo init`)
- Tokio as your async runtime
### Step 1: Install Toasty with the Turso driver

```
cargo add toasty --features turso
cargo add tokio --features full
cargo add uuid

```

### Step 2: Define your models

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
