# Rust crate selection and feature requirements

[Context index](rust-package-and-api-selection.md)

Turso offers two Rust crates:

|  | `turso` | `libsql` |
| --- | --- | --- |
| **Use case** | Local / embedded database, sync | Remote access, existing libSQL codebases |
| **Engine** | Turso Database (rewrite) | libSQL (SQLite fork) |
| **Concurrent writes** | Yes (MVCC) | Not supported |
| **Sync** | push/pull (local-first) | Embedded Replicas, where writes are forwarded to the cloud primary |
| **C compiler** | Not required | Required for `core`, `replication`, `encryption` features |

 **Starting a new project?** Use `turso` for local or embedded storage and sync. Use `libsql` with the `remote` feature for over-the-wire access that does not need a C compiler.
