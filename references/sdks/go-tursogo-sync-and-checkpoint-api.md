# Go tursogo sync and checkpoint API

Open when connecting a synchronized local database, pushing writes, pulling remote changes, checkpointing or inspecting sync statistics. Install `turso.tech/database/tursogo` and supply `Path`, `RemoteUrl` and `AuthToken` through `TursoSyncDbConfig`; see the [connection reference](go-tursogo-connection-queries-and-encryption.md).

The first connection seeds the local database from the remote database. Reads and writes remain local. `Push` uploads local changes; `Pull` retrieves remote updates. `Checkpoint` reduces local WAL growth while retaining sync metadata.

```go
package example

import (
    "context"
    "fmt"
    turso "turso.tech/database/tursogo"
)

func synchronize(ctx context.Context, config turso.TursoSyncDbConfig) error {
    syncDb, err := turso.NewTursoSyncDb(ctx, config)
    if err != nil {
        return err
    }
    db, err := syncDb.Connect(ctx)
    if err != nil {
        return err
    }
    defer db.Close()
    if err := syncDb.Push(ctx); err != nil {
        return err
    }
    changed, err := syncDb.Pull(ctx)
    if err != nil {
        return err
    }
    fmt.Printf("Pulled changes: %v\n", changed)
    if err := syncDb.Checkpoint(ctx); err != nil {
        return err
    }
    stats, err := syncDb.Stats(ctx)
    if err != nil {
        return err
    }
    fmt.Printf("CDC operations: %d\n", stats.CdcOperations)
    fmt.Printf("Main WAL size: %d\n", stats.MainWalSize)
    fmt.Printf("Network sent: %d bytes\n", stats.NetworkSentBytes)
    fmt.Printf("Network received: %d bytes\n", stats.NetworkReceivedBytes)
    fmt.Printf("Last pull: %d\n", stats.LastPullUnixTime)
    fmt.Printf("Last push: %d\n", stats.LastPushUnixTime)
    fmt.Printf("Revision: %s\n", stats.Revision)
    return nil
}
```

The caller handles the returned error. See [Turso Sync](https://docs.turso.tech/sync/usage) for bootstrap and conflict behavior. This helper closes its local database connection; it does not retry failed synchronization operations automatically.
