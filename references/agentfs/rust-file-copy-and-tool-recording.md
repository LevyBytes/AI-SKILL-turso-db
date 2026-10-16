# Rust file copy and tool recording

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](rust-installation-and-database-connection.md) · [Previous phase](rust-directories-metadata-and-removal.md) · [Continue](rust-tool-querying-and-cleanup.md)

Use the connection and async context from [Rust setup](rust-installation-and-database-connection.md). Version 0.1.0 has no `rename` or `copy_file` convenience method. A byte-content copy can be implemented with `read_file` and `write_file`, but does not preserve every metadata field or provide atomic rename semantics.

```rust
if let Some(data) = agent.fs.read_file("/template.md").await? {
    agent.fs.write_file("/new-report.md", &data).await?;
}
```

For a move implemented as copy then remove, remove the source only after the write succeeds, and account for failures between the two operations. Do not use this sequence where an atomic rename is required.

### Record a Completed Tool Call

`record` takes the tool name, Unix-second start/end timestamps, optional JSON parameters/result and an optional error string. It returns an integer record ID.

```rust
use serde_json::json;
use std::time::{SystemTime, UNIX_EPOCH};

let started_at = SystemTime::now().duration_since(UNIX_EPOCH)?.as_secs() as i64;
let data = agent.fs.read_file("/report.md").await?;
let completed_at = SystemTime::now().duration_since(UNIX_EPOCH)?.as_secs() as i64;
let id = agent.tools.record(
    "read_file",
    started_at,
    completed_at,
    Some(json!({"path": "/report.md"})),
    Some(json!({"found": data.is_some()})),
    None,
).await?;
```

For calls still in progress, use `start(name, parameters)` followed by `success(id, result)` or `error(id, message)` to finish the same record.
