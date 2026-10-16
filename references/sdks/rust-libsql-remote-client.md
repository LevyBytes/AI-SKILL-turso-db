# Rust libSQL remote client

[Context index](rust-package-and-api-selection.md)

### libsql (Remote)

 For over-the-wire Turso Cloud access, use the `libsql` crate with the `remote` feature. It is implemented as pure Rust HTTP, so no C compiler is needed.

### Installing

```
cargo add libsql --features remote

```

### Connecting

```
use libsql::Builder;

let url = std::env::var("TURSO_DATABASE_URL")?;
let token = std::env::var("TURSO_AUTH_TOKEN")?;

let db = Builder::new_remote(url, token).build().await?;
let conn = db.connect()?;

```

### Querying

```
let mut rows = conn.query("SELECT * FROM users WHERE id = ?1", [1]).await?;

```
