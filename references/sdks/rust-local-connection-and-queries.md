# Rust local connection and queries

[Context index](client-connection-and-vercel-workflows.md)

## Rust

### Step 1: Install

Add the Turso crate to your Rust project:

```
cargo add turso

```

### Step 2: Connect

Connect to a local SQLite database with this example:

```
use turso::Builder;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let db = Builder::new_local("sqlite.db").build().await?;
    let conn = db.connect()?;

    Ok(())
}

```

### Step 3: Create table

Create a table for users:

```
conn.execute(
    "CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT NOT NULL
    )",
    ()
).await?;

```

### Step 4: Insert data

Insert rows into the users table:

```
conn.execute("INSERT INTO users (username) VALUES (?)", ("alice",)).await?;
conn.execute("INSERT INTO users (username) VALUES (?)", ("bob",)).await?;

```

### Step 5: Query data

Read all users from the table:

```
let res = conn.query("SELECT * FROM users", ()).await?;
println!("{:?}", res);

```
