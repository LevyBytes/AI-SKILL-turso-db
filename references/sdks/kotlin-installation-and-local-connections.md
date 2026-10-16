# Kotlin installation and local connections

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](kotlin-remote-queries-and-replica-sync.md)

## Quickstart

The feature is still in technical preview. Use [Join us in Discord](https://discord.gg/turso) to share problems you encounter. For now, it works only with the Android Gradle Plugin, and full Kotlin support is planned. This Kotlin quickstart covers how to:
- Retrieve database credentials
- Include libSQL as a dependency in your Android Gradle project
- Open a connection to either a local or remote Turso database
- Execute a query using SQL
- Synchronize changes into a local database when needed (optional)
### Step 1: Retrieve database credentials

An existing database is required before continuing. If you do not already have one, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Place the credentials in the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Make sure database tokens are handled securely.

### Step 2: Install

Configure libsql as an implementation dependency in Gradle. This currently works only with the Android Gradle Plugin.

```
dependencies {
    implementation("tech.turso.libsql:libsql:0.1.0")
}

```

### Step 3: Connect

Start by creating a `Database` object, then open a `Connection` from it:

Embedded Replicas

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    path = "./local.db",
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

Local only

```
import tech.turso.libsql.Libsql

val db = Libsql.open(path = "./local.db")
val conn = db.connect()

```
