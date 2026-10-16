# Kotlin client configuration and queries

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](kotlin-parameters-batches-and-transactions.md)

## Installing

The feature remains in technical preview. Use [Join us in Discord](https://discord.gg/turso) to report problems. At this stage, it works only with the Android Gradle Plugin, with full Kotlin support still coming.

### Installing

 Configure libsql as an implementation dependency in Gradle:

```
dependencies {
    implementation("tech.turso.libsql:libsql:0.1.0")
}

```

### In-Memory Databases

 libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html) when persistence is not needed:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(":memory:")
val conn = db.connect()

```

### Local Development

 Local development can use an SQLite file:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(path = "./local.db")
val conn = db.connect()

```

### Embedded Replicas

 Embedded replicas can synchronize from the remote URL while delegating writes to the remote primary database:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    path = "./local.db",
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

### Manual Sync

 The `sync` function lets you manually synchronize the local database with its remote counterpart:

```
db.sync() // Call sync manually to update local database (only for EmbeddedReplicaDatabase)

```

### Simple query

 Pass a string into `query()` to run a SQL statement, with optional arguments when required:

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = ?", 1)
}

```
