# Kotlin remote queries and replica sync

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](kotlin-installation-and-local-connections.md)

Remote only

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

### Step 4: Execute

To run a SQL query against the existing database, call `execute()`:

```
db.connect().use {
    it.execute("INSERT INTO users (id) VALUES (1)")
}

```

Placeholders can be used when values need to be supplied separately:

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = ?", 1)
}

```

### Step 5: Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database unless `syncInterval` is already in use. Calling `sync` while `syncInterval` is enabled is still allowed:

```
db.sync()

```
