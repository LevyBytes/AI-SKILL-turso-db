# Go tursogo cloud sync quickstart

[Context index](go-sdk-workflows.md) · [Setup and preceding context](go-tursogo-local-quickstart.md)

### Step 3: Sync (push and pull)

When local data must synchronize with Turso Cloud, choose the sync driver:

```go
package main

import (
    "context"
    "log"
    "os"
    turso "turso.tech/database/tursogo"
)

func main() {
    if err := run(); err != nil { log.Fatal(err) }
}

func run() error {
    ctx := context.Background()
    syncDb, err := turso.NewTursoSyncDb(ctx, turso.TursoSyncDbConfig{
        Path: "app.db",
        RemoteUrl: os.Getenv("TURSO_DATABASE_URL"),
        AuthToken: os.Getenv("TURSO_AUTH_TOKEN"),
    })
    if err != nil { return err }
    db, err := syncDb.Connect(ctx)
    if err != nil { return err }
    defer db.Close()
    // The remote users(id, name) table must exist before this example.
    if _, err = db.ExecContext(ctx, "INSERT INTO users (name) VALUES (?)", "Bob"); err != nil {
        return err
    }
    if err = syncDb.Push(ctx); err != nil { return err }
    _, err = syncDb.Pull(ctx)
    return err
}
```

Reads and writes use the local database file, which keeps access fast and usable offline. Call `Push()` to upload local updates, and call `Pull()` to retrieve changes from the remote side. The [reference](https://docs.turso.tech/sdk/go/reference) covers checkpoint behavior, stats, and encryption, while [Turso Sync](https://docs.turso.tech/sync/usage) explains conflict handling and related topics. To try sync locally without a Turso Cloud account, run a local sync server:

```
tursodb :memory: --sync-server 127.0.0.1:8080

```

After that, use `http://127.0.0.1:8080` as `RemoteUrl` and omit `AuthToken`. Installation steps for `tursodb` are in the [Turso Database quickstart](https://docs.turso.tech/tursodb/quickstart).
