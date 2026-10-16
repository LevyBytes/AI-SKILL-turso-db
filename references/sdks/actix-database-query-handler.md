# Actix database query handler

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](actix-dependencies-and-database-credentials.md)

This Actix Web 4 example uses `libsql` 0.9.30, `dotenvy` and `serde` with `derive`. Configure `TURSO_DATABASE_URL` and `TURSO_AUTH_TOKEN`; optional `LOCAL_DB` chooses the replica file. Create `items(task TEXT)` before requesting `/items`.

```rust
use actix_web::{error::ErrorInternalServerError, web, App, Error, HttpResponse, HttpServer};
use libsql::{Builder, Database};
use serde::Serialize;

#[derive(Serialize)]
struct Item {
    task: String,
}

async fn get_items(db: web::Data<Database>) -> Result<HttpResponse, Error> {
    let conn = db.connect().map_err(ErrorInternalServerError)?;
    let mut rows = conn.query("SELECT task FROM items", ()).await
        .map_err(ErrorInternalServerError)?;
    let mut items = Vec::new();
    while let Some(row) = rows.next().await.map_err(ErrorInternalServerError)? {
        items.push(Item { task: row.get(0).map_err(ErrorInternalServerError)? });
    }
    Ok(HttpResponse::Ok().json(items))
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    dotenvy::dotenv().ok();
    let url = std::env::var("TURSO_DATABASE_URL").map_err(std::io::Error::other)?;
    let token = std::env::var("TURSO_AUTH_TOKEN").map_err(std::io::Error::other)?;
    let path = std::env::var("LOCAL_DB").unwrap_or_else(|_| "local.db".into());
    let db = Builder::new_remote_replica(path, url, token)
        .read_your_writes(true).build().await.map_err(std::io::Error::other)?;
    let state = web::Data::new(db);
    HttpServer::new(move || App::new()
        .app_data(state.clone())
        .route("/items", web::get().to(get_items)))
        .bind(("127.0.0.1", 8080))?
        .run().await
}
```

### Examples

### Turso + Actix Web Traffic Tracker

See the full source code
