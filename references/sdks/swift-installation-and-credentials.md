# Swift installation and credentials

[Context index](swift-sdk-workflows.md) · [Continue](swift-connections-queries-and-replica-sync.md)

## libSQL Swift

This feature is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues. This Swift quickstart covers how to:
- Retrieve database credentials
- Install the libSQL package
- Connect to a local or remote Turso database
- Execute a query using SQL
- Optionally sync changes into a local database
### Step 1: Retrieve database credentials

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

Make sure database tokens are handled securely.

### Step 2: Install

Start by adding `libsql` as an XCode package dependency from this repo:

### libSQL Swift

Build from source code, or add it to your SwiftPM dependencies:

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
