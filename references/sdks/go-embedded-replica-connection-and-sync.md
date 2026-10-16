# Go embedded replica connection and sync

[Context index](go-sdk-workflows.md) · [Continue](go-embedded-replica-encryption.md)

### go-libsql (libSQL)

 `go-libsql` is based on [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork that currently underpins Turso Cloud. It is production-ready, proven in real use, and best suited to existing codebases already using `go-libsql`. With `go-libsql` Embedded Replicas, reads happen locally. Writes are forwarded to the cloud primary and then copied back to the replica. Embedded Replicas are supported for production use. For new projects that need sync, prefer `tursogo` with `NewTursoSyncDb`; see the [quickstart](https://docs.turso.tech/sdk/go/quickstart).

### Embedded Replicas

 For workloads requiring **offline writes**, **bidirectional sync**, or **multi-writer convergence**, use `tursogo` with `NewTursoSyncDb`. Reads and writes both stay local, and synchronization is controlled directly with `Push()` / `Pull()`. [embedded replicas](https://docs.turso.tech/features/embedded-replicas) can synchronize data from the remote database into a local SQLite file and send writes through the remote primary database:

```
package main

import (
  "database/sql"
  "fmt"
  "log"
  "os"
  "path/filepath"

  "github.com/tursodatabase/go-libsql"
)

func main() {
    if err := run(); err != nil {
        log.Fatal(err)
    }
}

func run() error {
    dbName := "local.db"
    primaryUrl := "libsql://[DATABASE].turso.io"
    authToken := "..."

    dir, err := os.MkdirTemp("", "libsql-*")
    if err != nil {
        return fmt.Errorf("create temporary directory: %w", err)
    }
    defer os.RemoveAll(dir)

    dbPath := filepath.Join(dir, dbName)

    connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
      libsql.WithAuthToken(authToken),
    )
    if err != nil {
        return fmt.Errorf("create connector: %w", err)
    }
    defer connector.Close()

    db := sql.OpenDB(connector)
    defer db.Close()
    return nil
}

```

 Embedded Replicas require file system access.

#### Manual Sync

```
if err := connector.Sync(); err != nil {
    fmt.Println("Error syncing database:", err)
}

```

#### Periodic Sync

Import `time` and use this constructor alternative inside the setup function, checking `err` before opening the database.

```
syncInterval := time.Minute

connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithSyncInterval(syncInterval),
)

```

#### Read Your Writes

 After synchronization, the default behavior waits for the server to apply all of your changes before it returns, so later reads observe your own writes. That guarantee is more conservative but **much slower**, because every pending change must be processed by the server. If eventual consistency is acceptable, turn it off to make sync much faster:

```
connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithReadYourWrites(false),
)

```
