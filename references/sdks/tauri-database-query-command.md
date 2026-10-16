# Tauri database query command

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](tauri-database-dependencies-and-credentials.md)

### Step 4: Execute SQL

src-tauri/src/main.rs

```
use dotenvy::dotenv;
use libsql::Builder;
use serde::{Deserialize, Serialize};
use std::env;


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

    let db = Builder::new_remote_replica(db_path, sync_url, auth_token)
        .build().await?;

    let conn = db.connect()?;

    let mut results = conn
        .query("SELECT * FROM table_name", ())
        .await?;

    let mut items: Vec<Item> = Vec::new();
    while let Some(row) = results.next().await? {
        let item = Item {
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
