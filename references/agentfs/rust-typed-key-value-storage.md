# Rust typed key value storage

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](rust-installation-and-database-connection.md) · [Continue](rust-key-enumeration-and-file-contents.md)

Use the connection and async context from [Rust setup](rust-installation-and-database-connection.md). Version 0.1.0 serializes borrowed values with Serde and returns `Option<T>` when reading a key.

```rust
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
struct UserPreferences {
    theme: String,
    language: String,
}

agent.kv.set("user:preferences", &UserPreferences {
    theme: "dark".to_string(),
    language: "en".to_string(),
}).await?;
agent.kv.set("counter", &42).await?;
agent.kv.set("active", &true).await?;

let prefs: Option<UserPreferences> = agent.kv.get("user:preferences").await?;
if let Some(count) = agent.kv.get::<i32>("counter").await? {
    println!("Counter: {count}");
}
let theme = agent.kv.get::<String>("theme").await?
    .unwrap_or_else(|| "light".to_string());
agent.kv.delete("session:expired").await?;
```

The contracts are `set(key, &value)`, `get::<T>(key)` and `delete(key)`, each asynchronous and fallible. A missing key is `None`, distinct from a deserialization or storage error.
