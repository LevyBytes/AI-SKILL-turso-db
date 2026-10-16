# Toasty connections schema and queries

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](toasty-models-and-turso-dependencies.md)

### Step 3: Connect to Turso

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

To turn on Turso's MVCC journal for concurrent writers, add the direct dependency with `cargo add toasty-driver-turso`, matching the Toasty version in the project. Explicitly build that driver and pass it into `build()`:

```
use toasty_driver_turso::Turso;

let driver = Turso::file("app.db").concurrent_writes();

let db = toasty::Db::builder()
    .models(toasty::models!(crate::*))
    .build(driver)
    .await?;

```

With `concurrent_writes()` enabled, write-write conflicts appear as serialization failures; test `err.is_serialization_failure()` and retry the transaction.

### Step 4: Create the schema and query

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
