# Multiprocess bindings concurrency and limits

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](multiprocess-wal-setup-cli-and-rust.md)

### JavaScript / Node.js SDK

```
import { connect } from "@tursodatabase/database";

const db = await connect("mydb.db", {
    experimental: ["multiprocess_wal"],
});

```

### Python SDK

```
import turso

conn = turso.connect(
    "mydb.db",
    experimental_features="multiprocess_wal",
)

```

### Go SDK

```
db, err := turso.NewDatabase(turso.TursoDatabaseConfig{
    Path:                 "mydb.db",
    ExperimentalFeatures: "multiprocess_wal",
})

```

 Or via DSN:

```
db, err := sql.Open("turso", "mydb.db?experimental=multiprocess_wal")

```

### Concurrency Model

 Multi-process access preserves Turso’s WAL concurrency guarantees across processes:
- **Writers are serialized across processes.** At any moment, only one process (and one connection in that process) owns the writer slot. Writers elsewhere wait for release.
- **Readers never block writers.** Each active read transaction claims a reader slot and pins that snapshot's max-frame. A reader in one process is not invalidated by another process's writer.
- **Checkpointing is serialized across processes.** One checkpointer truncates the WAL at a time; readers with older pinned frames prevent early reclamation no matter which process owns those readers.
- **Snapshots are stable.** Every read transaction sees a consistent snapshot even while another process commits new frames.
 When one process performs schema changes (DDL), other processes pick them up on the next statement through the existing schema refresh path. Prepared statements in sibling processes can return `SchemaUpdated` and re-prepare, matching behavior across connections in one process.

### Mixing Modes

 A database cannot be opened at the same time in single-process mode by one process and multi-process mode by another. During open, Turso checks for an incompatible live opener and fails immediately:
- Opening without `multiprocess_wal` fails when another process has live `.tshm` authority:

>  Database is already open with experimental multiprocess WAL in another process

- Opening with `multiprocess_wal` fails when another process holds the legacy exclusive DB-file lock:

>  Database is already open without experimental multiprocess WAL in another process

 To change modes, first close every existing connection to the database, then reopen with the chosen configuration. The `.tshm` file may remain; Turso uses it again on the next multi-process open and reconstructs state from the WAL when needed. Read-only opens are advisory: if a read-only open requests `multiprocess_wal` and no `.tshm` file exists, Turso uses the legacy read-only WAL path instead of failing. That behavior lets read-only tools inspect a database that a single-process writer closed cleanly.

### Limitations

- The `.tshm` file format has a version and can change in later releases. When the format changes, existing `.tshm` files become invalid; Turso recreates them during open and does not perform cross-version migration.
- Attached databases (via [ATTACH DATABASE](https://docs.turso.tech/sql-reference/statements/attach-database)) use the main connection's multi-process setting.
- This feature is not currently available with MVCC (`BEGIN CONCURRENT`). Choose MVCC inside one process or multi-process WAL with the default isolation model; do not combine them.
- Multi-process mode concurrent-simulator and stress tests are limited to 64-bit Unix targets. Builds for Windows, WASM, and 32-bit platforms accept the flag but leave behavior unchanged.

### See Also

- [Experimental Features](https://docs.turso.tech/sql-reference/experimental-features) - enabling experimental features on every supported surface
- [Transactions](https://docs.turso.tech/sql-reference/statements/transactions) - WAL concurrency behavior and `BEGIN CONCURRENT`
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) — SQLite compatibility matrix
