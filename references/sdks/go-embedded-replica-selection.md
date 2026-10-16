# Go embedded replica selection

[Context index](go-sdk-workflows.md)

### Embedded Replicas (go-libsql)

 Embedded Replicas provide a local read copy of a Turso Cloud database for a Go application. Reads come from the local replica, while writes are routed to the cloud primary and then mirrored back. Embedded Replicas are supported for production use. For new sync-focused projects, prefer `tursogo` with `NewTursoSyncDb`: reads and writes both occur locally, synchronization is triggered with `Push()` / `Pull()`, and the wire format uses logical change-data-capture instead of page frames ([benchmark](https://turso.tech/blog/sync-benchmark)). The [reference](https://docs.turso.tech/sdk/go/reference) has the complete Embedded Replicas documentation for `go-libsql`.
