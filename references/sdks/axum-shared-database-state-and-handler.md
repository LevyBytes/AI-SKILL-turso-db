# Axum shared database state and handler

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](axum-dependencies-and-database-query.md)

This example targets **Axum 0.6.20**, whose `Server::bind` API is used below. Use Tokio with macros/runtime support, libsql 0.9.30 and Serde with `derive`. Export the database credentials and create `users(id, name)` before serving requests.

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
