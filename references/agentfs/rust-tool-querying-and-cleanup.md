# Rust tool querying and cleanup

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](rust-installation-and-database-connection.md) · [Previous phase](rust-file-copy-and-tool-recording.md)

Use the connection and async context from [Rust setup](rust-installation-and-database-connection.md). Version 0.1.0 provides `recent`, `get`, `stats_for` and `stats`; it does not expose `ToolListOptions`, `tools.delete` or `tools.clear`.

```rust
let recent = agent.tools.recent(Some(10)).await?;
for call in &recent {
    println!("{}: {}", call.id, call.name);
}
if let Some(call) = agent.tools.get(42).await? {
    println!("Tool: {}", call.name);
    println!("Duration: {:?} ms", call.duration_ms);
}
let search_stats = agent.tools.stats_for("web_search").await?;
let all_stats = agent.tools.stats().await?;
```

For name and time filters, query `tool_calls` with parameter binding through `agent.get_connection()`. `started_at` and `completed_at` use Unix seconds; duration is milliseconds and can be absent for an unfinished record.

```rust
let conn = agent.get_connection();
let mut rows = conn.query(
    "SELECT id, name FROM tool_calls WHERE name = ? AND started_at >= ? ORDER BY started_at DESC LIMIT ?",
    ("web_search", 1704447825_i64, 100_i64),
).await?;
while let Some(row) = rows.next().await? {
    println!("{:?}", row.get_value(0));
}
```

Audit deletion is an explicit SQL maintenance operation. Back up the database and define the retention boundary before deleting records; routine inspection should leave the audit trail intact.

- Open when checking the exact API: [pinned SDK source](https://github.com/tursodatabase/agentfs/tree/3c693e5b3890c0b1afa16a1576e3a91097881ac3/sdk/rust).
- Open when reporting an SDK defect: [AgentFS issues](https://github.com/tursodatabase/agentfs/issues).
- Open when seeking project support: [Turso community](https://tur.so/discord).
