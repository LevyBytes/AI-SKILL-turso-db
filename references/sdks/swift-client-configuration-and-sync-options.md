# Swift client configuration and sync options

[Context index](swift-sdk-workflows.md) · [Continue](swift-statements-parameters-and-write-visibility.md)

## Installing

This feature is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues.

### Installing

 Start by adding `libsql` as an XCode package dependency from this repo:

### libSQL Swift

Build from source code Or add it to your SwiftPM dependencies:

```
import PackageDescription

// Dependency excerpt for an existing Package.swift, not a complete manifest.
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

let db = try Database(":memory:")
let conn = try db.connect()

```

### Local Development

 For local development, use an SQLite file:

```
import Libsql

let db = try Database("local.db")
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
    authToken: "TURSO_AUTH_TOKEN",
    syncInterval: 3000 // Milliseconds: sync every 3 seconds
)

let conn = try db.connect()

```
