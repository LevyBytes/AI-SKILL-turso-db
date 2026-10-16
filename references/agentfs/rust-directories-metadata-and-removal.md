# Rust directories metadata and removal

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](rust-installation-and-database-connection.md) · [Previous phase](rust-key-enumeration-and-file-contents.md) · [Continue](rust-file-copy-and-tool-recording.md)

Use the connection and async context from [Rust setup](rust-installation-and-database-connection.md). In 0.1.0, create nested directories one level at a time with `mkdir`; their parents must exist.

```rust
if agent.fs.stat("/reports").await?.is_none() {
    agent.fs.mkdir("/reports").await?;
}
if let Some(entries) = agent.fs.readdir("/reports").await? {
    let mut files = Vec::new();
    for name in &entries {
        let path = format!("/reports/{name}");
        if let Some(stat) = agent.fs.stat(&path).await? {
            println!("{name}: {} bytes, modified {}", stat.size, stat.mtime);
            if stat.is_file() {
                files.push(name.clone());
            }
        }
    }
    println!("{} directory entries, {} files", entries.len(), files.len());
}
```

`readdir` returns optional names, not entry metadata objects. Call `stat` for each path. `stat` follows symlinks; `lstat` inspects the link itself. Both return `Option<Stats>`.

```rust
if let Some(stat) = agent.fs.stat("/report.md").await? {
    println!("Size: {} bytes", stat.size);
    println!("Mode: {:o}", stat.mode);
    println!("Modified: {}", stat.mtime);
    println!("Metadata changed: {}", stat.ctime);
    println!("File: {}, directory: {}", stat.is_file(), stat.is_directory());
}
if agent.fs.stat("/old-report.md").await?.is_some() {
    agent.fs.remove("/old-report.md").await?;
}
```

`ctime` is inode metadata-change time, not a portable creation timestamp. Version 0.1.0 uses `remove` for files or empty directories. Recursive removal must explicitly walk children before removing a directory; do not call nonexistent `rm_rf` or `mkdir_p` methods.
