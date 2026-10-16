# Go remote libSQL quickstart

[Context index](go-sdk-workflows.md)

### Remote Access (Over-the-Wire)

 For applications that should talk to a Turso Cloud database directly over the network, such as web servers or serverless functions, use `libsql-client-go`. It reaches the database through the libSQL wire protocol without needing a local file, and it is pure Go. Most applications should instead use a synchronized local database through `NewTursoSyncDb`, because that gives faster reads, offline use, and reduced latency. Direct remote access fits cases where keeping a local database file is not possible, such as stateless serverless runtimes.

### Step 1: Retrieve database credentials

An existing database is required before continuing. If you do not have one yet, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Place the credential values in `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

These values should be kept in environment variables.

### Step 2: Install

```
go get github.com/tursodatabase/libsql-client-go/libsql

```

### Step 3: Connect and query

Use the complete [remote query program](go-remote-query-program.md). It expects the `users(id, name)` table and credentials exported into the process environment; a `.env` file alone is not loaded by Go.
