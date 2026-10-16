# Rust key enumeration and file contents

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](rust-installation-and-database-connection.md) · [Previous phase](rust-typed-key-value-storage.md) · [Continue](rust-directories-metadata-and-removal.md)

Use the connection and async context from [Rust setup](rust-installation-and-database-connection.md). In 0.1.0, `keys()` returns all key names. Prefix filtering and paging are application work; there is no SDK `ListOptions` cursor contract.

```rust
let mut keys = agent.kv.keys().await?;
keys.retain(|key| key.starts_with("user:"));
keys.sort();
let first_page: Vec<_> = keys.iter().take(10).collect();
let second_page: Vec<_> = keys.iter().skip(10).take(10).collect();
```

This pages one in-memory snapshot. For large key sets use an explicit ordered SQL query on `kv_store` through `get_connection()`. To clear stored values intentionally, enumerate keys and call `kv.delete` for each; that sequence is not an atomic clear operation.

### File Contents

Create parent directories before writing nested paths. `read_file` returns `Option<Vec<u8>>`; handle a missing file separately from an I/O error.

```rust
agent.fs.write_file("/report.md", b"# Report\nContent...").await?;
let content = "Hello, World!";
agent.fs.write_file("/greeting.txt", content.as_bytes()).await?;

if agent.fs.stat("/images").await?.is_none() {
    agent.fs.mkdir("/images").await?;
}
let image_data = std::fs::read("./chart.png")?;
agent.fs.write_file("/images/chart.png", &image_data).await?;

if let Some(data) = agent.fs.read_file("/report.md").await? {
    let text = String::from_utf8(data)?;
    println!("{text}");
}
if let Some(data) = agent.fs.read_file("/config.json").await? {
    let config: serde_json::Value = serde_json::from_slice(&data)?;
    println!("{config}");
}
```
