# Rust installation and database connection

[Context index](agentfs-language-sdks.md) · [Continue](rust-typed-key-value-storage.md)

These examples target `agentfs-sdk` 0.1.0. Use its actual crate name, `agentfs_sdk`, in Rust imports. Run later snippets inside an async function returning `anyhow::Result<()>`, with the `agent` connection shown here.

```toml
[dependencies]
agentfs-sdk = "=0.1.0"
anyhow = "1"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

```rust
use agentfs_sdk::AgentFS;
use anyhow::Result;

#[tokio::main]
async fn main() -> Result<()> {
    // The parent directory must exist before opening a database path.
    std::fs::create_dir_all(".agentfs")?;
    let agent = AgentFS::new(".agentfs/my-agent.db").await?;
    agent.kv.set("key", &"value").await?;
    agent.fs.write_file("/file.txt", b"data").await?;
    let id = agent.tools.start("write_file", None).await?;
    agent.tools.success(id, None).await?;
    Ok(())
}
```

For an ephemeral database, use `AgentFS::new(":memory:").await?` as an alternative connection. Version 0.1.0 selects storage by database path; it does not expose the later `AgentFSOptions` interface.

The public `kv`, `fs` and `tools` fields provide key-value, filesystem and audit operations. `agent.get_connection()` returns the shared underlying connection for explicit SQL.

Open when checking exact version-specific behavior: [Rust SDK 0.1.0 source](https://github.com/tursodatabase/agentfs/tree/3c693e5b3890c0b1afa16a1576e3a91097881ac3/sdk/rust).
