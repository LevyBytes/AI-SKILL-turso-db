# Tauri database dependencies and credentials

[Context index](rust-framework-and-connection-workflows.md) · [Continue](tauri-database-query-command.md)

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Tauri app - [learn more](https://tauri.app/v1/guides/getting-started/setup)
### Step 1: Add these crates to the Tauri project's dependencies

src-tauri/Cargo.toml

```
[dependencies]
serde_json = "1.0"
serde = { version = "1.0", features = ["derive"] }
reqwest = { version = "0.11.22", features = ["json", "blocking"] }
dotenvy = "0.15.7"
tokio = { version = "1", features = ["full"] }
libsql = "=0.9.30"
tracing-subscriber = "0.3"
tracing = "0.1.40"


```

### Step 2: Fetch Rust dependencies from inside the `src-tauri` directory.

```
cargo fetch

```

### Step 3: Configure database credentials

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
