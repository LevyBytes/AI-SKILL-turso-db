# Rocket todo query application

Use Rocket 0.5 with the `json` feature, libsql 0.9.30 and dotenvy. Configure the two database credential variables from [Rocket setup](rocket-database-query-and-route.md), and create `todos(task TEXT)` before querying.

```rust
use libsql::Builder;
use rocket::{get, launch, routes, http::Status};
use rocket::serde::{json::Json, Serialize};

#[derive(Serialize)]
#[serde(crate = "rocket::serde")]
struct Todo {
    task: String,
}

#[get("/todos")]
async fn get_todos() -> Result<Json<Vec<Todo>>, Status> {
    let url = std::env::var("TURSO_DATABASE_URL")
        .map_err(|_| Status::InternalServerError)?;
    let token = std::env::var("TURSO_AUTH_TOKEN")
        .map_err(|_| Status::InternalServerError)?;
    let db = Builder::new_remote(url, token).build().await
        .map_err(|_| Status::InternalServerError)?;
    let conn = db.connect().map_err(|_| Status::InternalServerError)?;
    let mut rows = conn.query("SELECT task FROM todos", ()).await
        .map_err(|_| Status::InternalServerError)?;
    let mut todos = Vec::new();
    while let Some(row) = rows.next().await.map_err(|_| Status::InternalServerError)? {
        todos.push(Todo { task: row.get(0).map_err(|_| Status::InternalServerError)? });
    }
    Ok(Json(todos))
}

#[launch]
fn rocket() -> _ {
    dotenvy::dotenv().ok();
    rocket::build().mount("/", routes![get_todos])
}
```
