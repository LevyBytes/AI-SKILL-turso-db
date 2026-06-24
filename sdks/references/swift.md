# Quickstart, Reference

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: libSQL Swift; Installing.

## libSQL Swift

This feature is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues. This Swift quickstart covers how to:
- Retrieve database credentials
- Install the libSQL package
- Connect to a local or remote Turso database
- Execute a query using SQL
- Optionally sync changes into a local database
 1

Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Make sure database tokens are handled securely.2

Install

Start by adding `libsql` as an XCode package dependency from this repo:

### libSQL Swift

Build from source codeOr add it to your SwiftPM dependencies:

```
import PackageDescription

let package = Package(
    // ...
    dependencies: [
        .package(url: "https://github.com/tursodatabase/libsql-swift", from: "0.1.1"),
    ],
    // ...
)

```

3

Connect

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

let db = try Database(
    path: "./local.db",
)

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

4

Execute

Run a SQL query against the existing database with `query()`, or use `execute()` when the statement should not return rows:

```
let rows = try conn.query("SELECT * FROM users")

```

Placeholders can be used when values need to be bound:

```
try conn.query("SELECT * FROM users WHERE id = ?", 1)

```

5

Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database, unless `sync_interval` is handling that for you. Calling `sync` while `sync_interval` is enabled is still allowed:

```
try db.sync()

```

## Installing

This feature is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues.

### Installing

 Start by adding `libsql` as an XCode package dependency from this repo:

### libSQL Swift

Build from source code Or add it to your SwiftPM dependencies:

```
import PackageDescription

let package = Package(
    // ...
    dependencies: [
        .package(url: "https://github.com/tursodatabase/libsql-swift", from: "0.1.1"),
    ],
    // ...
)

```

### In-Memory Databases

 libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html) when persistence is not required:

```
import Libsql

let db = Database(":memory:")
let conn = try db.connect()

```

### Local Development

 For local development, use an SQLite file:

```
import Libsql

let db = Database("local.db")
let conn = try db.connect()

```

### Embedded Replicas

 Embedded replicas can synchronize from the remote URL while sending writes to the remote primary database:

```
import Libsql

let db = try Database(
    path: "./local.db",
    url: "TURSO_DATABASE_URL",
    authToken: "TURSO_AUTH_TOKEN"
)

let conn = try db.connect()

```

### Manual Sync

 Use the `sync` function to manually synchronize the local database with its remote counterpart:

```
try db.sync() // Call sync manually to update local database

```

### Sync Interval

 The `syncInterval` parameter sets a background interval for automatic database synchronization:

```
import Libsql

let db = try Database(
    path: "./local.db",
    url: "TURSO_DATABASE_URL",
    authToken: "TURSO_AUTH_TOKEN"
    syncInterval: 300 // Sync every 3 seconds
)

let conn = try db.connect()

```

### Read Your Own Writes

 The `readYourWrites` parameter makes writes immediately visible to later reads from the same connection. This behavior is **enabled by default**, which helps preserve writer-side consistency in distributed systems. Pass `false` to disable it:

```
import Libsql

let db = try Database(
    path: "./local.db",
    url: <LIBSQL_URL>,
    authToken: <LIBSQL_AUTH_TOKEN>,
    readYourWrites: false
)

let conn = try db.connect()

```

### Simple query

 Pass a string to `query()` to run a SQL statement, with optional arguments when needed:

```
try conn.execute("INSERT INTO users VALUES (?)", [1])

```

### Prepared Statements

 Use `prepare()` to create a cached statement, then execute it with `query()`:

```
let stmt = try conn.prepare("SELECT * FROM users WHERE id = ?")
stmt.bind([1])
stmt.query()

```

### Placeholders

 libSQL supports positional and named placeholders inside SQL statements:

```
try conn.query("SELECT * FROM users WHERE id = ?", [1])

```
