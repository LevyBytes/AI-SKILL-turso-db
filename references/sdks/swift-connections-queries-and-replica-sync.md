# Swift connections queries and replica sync

[Context index](swift-sdk-workflows.md) · [Setup and preceding context](swift-installation-and-credentials.md)

### Step 3: Connect

Create a `Database` object first, then open a `Connection` from it:

Embedded Replicas

```
import Libsql

let db = try Database(
    path: "./local.db",
    url: "TURSO_DATABASE_URL",
    authToken: "TURSO_AUTH_TOKEN",
    syncInterval: 300
)

let conn = try db.connect()

```

Local only

```
import Libsql

let db = try Database("./local.db")

let conn = try db.connect()

```

Remote only

```
import Libsql

let db = try Database(
    url: "TURSO_DATABASE_URL",
    authToken: "TURSO_AUTH_TOKEN"
)

let conn = try db.connect()

```

### Step 4: Execute

Run a SQL query against the existing database with `query()`, or use `execute()` when the statement should not return rows:

```
let rows = try conn.query("SELECT * FROM users")

```

Placeholders can be used when values need to be bound:

```
try conn.query("SELECT * FROM users WHERE id = ?", [1])

```

### Step 5: Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database, unless `sync_interval` is handling that for you. Calling `sync` while `sync_interval` is enabled is still allowed:

```
try db.sync()

```
