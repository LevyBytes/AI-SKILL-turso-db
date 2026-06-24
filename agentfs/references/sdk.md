# Python, Rust, Typescript

> Agentfs reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Installation.

## Installation

The AgentFS Python SDK offers async APIs for AI agents that need durable storage.

### Installation

```
pip install agentfs-sdk

```

### Quick Start

```
import asyncio
from agentfs_sdk import AgentFS, AgentFSOptions

async def main():
    # Open an agent filesystem
    agent = await AgentFS.open(AgentFSOptions(id='my-agent'))

    # Use key-value store
    await agent.kv.set('config', {'debug': True})
    config = await agent.kv.get('config')

    # Use filesystem
    await agent.fs.write_file('/notes.txt', 'Hello, AgentFS!')
    content = await agent.fs.read_file('/notes.txt')

    # Track tool calls
    call_id = await agent.tools.start('search', {'query': 'Python'})
    await agent.tools.success(call_id, {'results': ['result1']})

    await agent.close()

asyncio.run(main())

```

### Opening a Filesystem

### By Agent ID

 Initializes the database under `.agentfs/{id}.db`:

```
agent = await AgentFS.open(AgentFSOptions(id='my-agent'))

```

### By Path

 Specify a custom database path:

```
agent = await AgentFS.open(AgentFSOptions(path='./data/mydb.db'))

```

### Context Manager

 Automatically closes the database:

```
async with await AgentFS.open(AgentFSOptions(id='my-agent')) as agent:
    await agent.kv.set('key', 'value')
    # Database is closed when exiting

```

### Key-Value Store

 Basic key-value persistence using JSON serialization.

### Set a Value

```
await agent.kv.set('user:123', {'name': 'Alice', 'age': 30})
await agent.kv.set('counter', 42)
await agent.kv.set('active', True)

```

### Get a Value

```
user = await agent.kv.get('user:123')
# Returns None if key doesn't exist

```

### List Keys

```
# List all keys with prefix
keys = await agent.kv.list('user:')
# Returns: ['user:123', 'user:456', ...]

```

### Delete a Key

```
await agent.kv.delete('user:123')

```

### Filesystem

 POSIX-like filesystem operations.

### Write a File

 Creates parent directories automatically:

```
await agent.fs.write_file('/data/config.json', '{"key": "value"}')

# Write bytes
await agent.fs.write_file('/data/image.png', image_bytes)

```

### Read a File

```
# Read as string (default)
content = await agent.fs.read_file('/data/config.json')

# Read as bytes
data = await agent.fs.read_file('/data/image.png', encoding=None)

```

### List Directory

```
entries = await agent.fs.readdir('/data')
# Returns: ['config.json', 'image.png', ...]

```

### Get File Stats

```
stats = await agent.fs.stat('/data/config.json')
print(f"Size: {stats.size} bytes")
print(f"Modified: {stats.mtime}")
print(f"Is file: {stats.is_file()}")
print(f"Is directory: {stats.is_dir()}")

```

### Delete a File

```
await agent.fs.delete_file('/data/config.json')

```

### Create Directory

```
await agent.fs.mkdir('/data/subdir')

```

### Tool Calls

 Capture tool/function calls so they can be reviewed during debugging and audits.

### Start and Complete

```
# Start a tool call
call_id = await agent.tools.start('search', {'query': 'Python'})

# ... perform the operation ...

# Mark as successful
await agent.tools.success(call_id, {'results': ['result1', 'result2']})

# Or mark as failed
await agent.tools.error(call_id, 'Connection timeout')

```

### Record Completed Call

 When both the start time and end time are available:

```
await agent.tools.record(
    name='search',
    started_at=1234567890.0,
    completed_at=1234567892.0,
    parameters={'query': 'Python'},
    result={'results': ['result1']}
)

```

### Query Tool Calls

```
# Get by name
calls = await agent.tools.get_by_name('search', limit=10)

# Get recent calls
recent = await agent.tools.get_recent(since=1234567890.0)

# Get by ID
call = await agent.tools.get(42)

```

### Get Statistics

```
stats = await agent.tools.get_stats()
for stat in stats:
    print(f"{stat.name}:")
    print(f"  Total calls: {stat.total_calls}")
    print(f"  Successful: {stat.successful}")
    print(f"  Failed: {stat.failed}")
    print(f"  Avg duration: {stat.avg_duration_ms:.2f}ms")

```

### Complete Example

```
import asyncio
import time
from agentfs_sdk import AgentFS, AgentFSOptions

async def main():
    async with await AgentFS.open(AgentFSOptions(id='demo-agent')) as agent:
        # Store configuration
        await agent.kv.set('agent:config', {
            'model': 'gpt-4',
            'temperature': 0.7
        })

        # Create a research document
        research = """
        # Research Notes

        ## Topic: AgentFS

        AgentFS provides persistent storage for AI agents.
        """

        await agent.fs.write_file('/research/agentfs.md', research)

        # Track a simulated tool call
        call_id = await agent.tools.start('web_search', {
            'query': 'AgentFS documentation'
        })

        # Simulate some work
        await asyncio.sleep(0.1)

        await agent.tools.success(call_id, {
            'results_count': 5,
            'top_result': 'https://docs.turso.tech/agentfs'
        })

        # List what we created
        files = await agent.fs.readdir('/research')
        print(f"Files: {files}")

        # Get tool stats
        stats = await agent.tools.get_stats()
        for stat in stats:
            print(f"{stat.name}: {stat.total_calls} calls")

asyncio.run(main())

```

### API Reference

### AgentFS

| Method | Description |
| --- | --- |
| `AgentFS.open(options)` | Create or access an agent filesystem |
| `agent.close()` | Close the database connection |
| `agent.kv` | Key-value store interface |
| `agent.fs` | Filesystem interface |
| `agent.tools` | Tool calls interface |

### AgentFSOptions

| Property | Type | Description |
| --- | --- | --- |
| `id` | `str` | Agent identifier (creates `.agentfs/{id}.db`) |
| `path` | `str` | Custom database path |

### KvStore

| Method | Description |
| --- | --- |
| `set(key, value)` | Store a value |
| `get(key)` | Retrieve a value |
| `delete(key)` | Delete a key |
| `list(prefix)` | List keys with prefix |

### Filesystem

| Method | Description |
| --- | --- |
| `write_file(path, content)` | Write file contents |
| `read_file(path, encoding)` | Read file contents |
| `readdir(path)` | List directory entries |
| `stat(path)` | Get file metadata |
| `delete_file(path)` | Delete a file |
| `mkdir(path)` | Create directory |

### ToolCalls

| Method | Description |
| --- | --- |
| `start(name, params)` | Start tracking a call |
| `success(id, result)` | Mark call as successful |
| `error(id, message)` | Mark call as failed |
| `record(...)` | Record a completed call |
| `get(id)` | Get call by ID |
| `get_by_name(name, limit)` | Query by tool name |
| `get_recent(since)` | Get recent calls |
| `get_stats()` | Get usage statistics |

### Next Steps

### OpenAI Agents

How to use AgentFS together with OpenAI Agents SDK

### LlamaIndex

Using AgentFS with LlamaIndex

## Installation

Use the AgentFS Rust SDK to build fast AI agents with persistent state.

### Installation

 Include AgentFS in your `Cargo.toml`:

```
[dependencies]
agentfs = "0.1"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"

```

### Quick Start

```
use agentfs::{AgentFS, AgentFSOptions};
use anyhow::Result;

#[tokio::main]
async fn main() -> Result<()> {
    // Persistent storage with identifier
    let agent = AgentFS::open(AgentFSOptions::with_id("my-agent")).await?;
    // Creates: .agentfs/my-agent.db

    // Or use ephemeral in-memory database
    let ephemeral = AgentFS::open(AgentFSOptions::ephemeral()).await?;

    // Use the three main APIs
    agent.kv.set("key", "value").await?;              // Key-value store
    agent.fs.write_file("/file.txt", b"data").await?; // Filesystem
    agent.tools.record(...).await?;                   // Tool tracking

    Ok(())
}

```

### Core APIs

### AgentFS Struct

 Primary access point for every AgentFS operation.

#### `AgentFS::open(options: AgentFSOptions)`

 Opens an existing AgentFS database or creates a new one.

```
use agentfs::{AgentFS, AgentFSOptions};

// Persistent storage with identifier
let agent = AgentFS::open(AgentFSOptions::with_id("my-agent")).await?;
// Creates: .agentfs/my-agent.db

// Ephemeral in-memory database
let ephemeral = AgentFS::open(AgentFSOptions::ephemeral()).await?;

```

 **AgentFSOptions Configuration:**

```
pub struct AgentFSOptions {
    /// Optional unique identifier for the agent.
    /// - If Some(id): Creates persistent storage at `.agentfs/{id}.db`
    /// - If None: Uses ephemeral in-memory database
    pub id: Option<String>,
}

impl AgentFSOptions {
    /// Create options for a persistent agent with the given ID
    pub fn with_id(id: impl Into<String>) -> Self;

    /// Create options for an ephemeral in-memory agent
    pub fn ephemeral() -> Self;
}

```

#### Fields

- `kv`: Key-value store interface
- `fs`: Filesystem interface
- `tools`: Tool call tracking interface
- `db`: Direct handle for the underlying Turso database

### Key-Value Store API

 High-speed typed storage backed by Serde serialization.

#### `kv.set<T: Serialize>(key: &str, value: T)`

 Store any serializable value.

```
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize)]
struct UserPreferences {
    theme: String,
    language: String,
}

// Store structured data
agent.kv.set("user:preferences", UserPreferences {
    theme: "dark".to_string(),
    language: "en".to_string(),
}).await?;

// Store primitives
agent.kv.set("counter", 42).await?;
agent.kv.set("active", true).await?;

```

#### `kv.get<T: DeserializeOwned>(key: &str) -> Option<T>`

 Retrieve and deserialize values.

```
// Get with type inference
let prefs: Option<UserPreferences> = agent.kv.get("user:preferences").await?;

// Handle missing values
if let Some(count) = agent.kv.get::<i32>("counter").await? {
    println!("Counter: {}", count);
}

// With default
let theme = agent.kv.get::<String>("theme").await?.unwrap_or("light".to_string());

```

#### `kv.delete(key: &str)`

 Remove a key-value pair.

```
agent.kv.delete("session:expired").await?;

```

#### `kv.list(options: ListOptions)`

 Return keys using pagination and filter options.

```
use agentfs::ListOptions;

// List all keys
let all_keys = agent.kv.list(ListOptions::default()).await?;

// Filter by prefix
let user_keys = agent.kv.list(ListOptions {
    prefix: Some("user:".to_string()),
    limit: Some(100),
    cursor: None,
}).await?;

// Paginate
let page1 = agent.kv.list(ListOptions {
    limit: Some(10),
    ..Default::default()
}).await?;

let page2 = agent.kv.list(ListOptions {
    limit: Some(10),
    cursor: page1.cursor,
    ..Default::default()
}).await?;

```

#### `kv.clear()`

 Remove all key-value pairs.

```
agent.kv.clear().await?;

```

### Filesystem API

 Filesystem operations with POSIX-style behavior for agent data management.

#### `fs.write_file(path: &str, data: &[u8])`

 Write byte data into a file and create missing parent directories when required.

```
// Write text
agent.fs.write_file("/report.md", b"# Report\nContent...").await?;

// Write string
let content = "Hello, World!";
agent.fs.write_file("/greeting.txt", content.as_bytes()).await?;

// Write binary
let image_data = std::fs::read("./chart.png")?;
agent.fs.write_file("/images/chart.png", &image_data).await?;

```

#### `fs.read_file(path: &str) -> Vec<u8>`

 Read file contents as bytes.

```
// Read as bytes
let data = agent.fs.read_file("/report.md").await?;

// Convert to string
let text = String::from_utf8(data)?;

// Parse JSON
let json_data = agent.fs.read_file("/config.json").await?;
let config: serde_json::Value = serde_json::from_slice(&json_data)?;

```

#### `fs.mkdir(path: &str)`

 Make a directory, with optional creation of any missing parents.

```
// Create single directory
agent.fs.mkdir("/reports").await?;

// Create nested directories
agent.fs.mkdir_p("/reports/2024/Q4").await?;

```

#### `fs.readdir(path: &str) -> Vec<DirEntry>`

 List directory contents with metadata.

```
let entries = agent.fs.readdir("/reports").await?;

for entry in entries {
    println!("Name: {}", entry.name);
    println!("Type: {:?}", entry.entry_type);
    println!("Size: {} bytes", entry.size);
    println!("Modified: {:?}", entry.modified);
}

// Filter by type
let files: Vec<_> = entries
    .iter()
    .filter(|e| e.entry_type == EntryType::File)
    .collect();

```

#### `fs.stat(path: &str) -> FileStat`

 Get file or directory metadata.

```
let stat = agent.fs.stat("/report.md").await?;

println!("Size: {} bytes", stat.size);
println!("Type: {:?}", stat.file_type);
println!("Created: {:?}", stat.created);
println!("Modified: {:?}", stat.modified);
println!("Permissions: {:o}", stat.mode);

if stat.is_file() {
    println!("It's a file");
} else if stat.is_directory() {
    println!("It's a directory");
}

```

#### `fs.exists(path: &str) -> bool`

 Determine whether a file or directory is present.

```
if agent.fs.exists("/config.json").await? {
    let config = agent.fs.read_file("/config.json").await?;
}

```

#### `fs.rm(path: &str)`

 Delete a file or a directory that is empty.

```
// Remove file
agent.fs.rm("/old-report.md").await?;

// Remove empty directory
agent.fs.rm("/empty-dir").await?;

// Remove directory tree
agent.fs.rm_rf("/old-data").await?;

```

#### `fs.rename(old_path: &str, new_path: &str)`

 Rename or move directories and files.

```
// Rename file
agent.fs.rename("/draft.md", "/final.md").await?;

// Move to different directory
agent.fs.rename("/temp/data.json", "/archive/data.json").await?;

```

#### `fs.copy_file(src: &str, dest: &str)`

 Duplicate a file at another path.

```
agent.fs.copy_file("/template.md", "/new-report.md").await?;

```

### Tool Call Tracking API

 Store and search agent tool invocation records.

#### `tools.record()`

 Store one tool invocation, including timing details and I/O data.

```
use agentfs::ToolCall;
use chrono::Utc;
use serde_json::json;

let start = Utc::now();
// ... perform operation ...
let end = Utc::now();

agent.tools.record(ToolCall {
    name: "web_search".to_string(),
    started_at: start,
    ended_at: end,
    input: json!({
        "query": "rust async programming",
        "max_results": 10
    }),
    output: json!({
        "results_count": 8,
        "success": true
    }),
}).await?;

```

#### `tools.list(options: ToolListOptions)`

 Query recorded tool calls.

```
use agentfs::ToolListOptions;
use chrono::{Duration, Utc};

// Get recent calls
let recent = agent.tools.list(ToolListOptions {
    limit: Some(10),
    ..Default::default()
}).await?;

// Filter by name
let searches = agent.tools.list(ToolListOptions {
    name: Some("web_search".to_string()),
    limit: Some(100),
    ..Default::default()
}).await?;

// Time range query
let last_hour = agent.tools.list(ToolListOptions {
    since: Some(Utc::now() - Duration::hours(1)),
    until: Some(Utc::now()),
    ..Default::default()
}).await?;

```

#### `tools.get(id: &str) -> Option<ToolCall>`

 Fetch information for one tool call by id.

```
if let Some(call) = agent.tools.get("call_123abc").await? {
    let duration = call.ended_at - call.started_at;
    println!("Tool: {}", call.name);
    println!("Duration: {}ms", duration.num_milliseconds());
}

```

#### `tools.delete(id: &str)`

 Remove a tool call record.

```
agent.tools.delete("call_123abc").await?;

```

#### `tools.clear()`

 Remove all tool call records.

```
agent.tools.clear().await?;

```

### Support

- [GitHub Issues](https://github.com/tursodatabase/agentfs/issues) - Report bugs or request features
- [Discord](https://tur.so/discord) - Help from the community
- [Examples](https://github.com/tursodatabase/agentfs/tree/main/examples/rust) - Sample applications

## Installation

Use the AgentFS TypeScript SDK to create stateful AI agents for Node.js and browser runtimes.

### Installation

-  npm
-  bun
-  deno

```
npm install agentfs-sdk

```

```
bun add agentfs-sdk

```

```
import { AgentFS } from "npm:agentfs-sdk";

```

### Quick Start

```
import { AgentFS } from 'agentfs-sdk';

// Persistent storage with identifier
const agent = await AgentFS.open({ id: 'my-agent' });
// Creates: .agentfs/my-agent.db

// Or use ephemeral in-memory database
const ephemeralAgent = await AgentFS.open();

// Use the three main APIs
// Key-value store
await agent.kv.set('key', 'value');
// Filesystem
await agent.fs.writeFile('/file.txt', 'data');
// Tool tracking
await agent.tools.record(...);

```

### Core APIs

### AgentFS Class

 Primary access point for every AgentFS operation.

#### `AgentFS.open(options?: AgentFSOptions)`

 Opens an AgentFS database if present, or creates one.

```
interface AgentFSOptions {
  /**
   * Optional unique identifier for the agent.
   * - If provided: Creates persistent storage at `.agentfs/{id}.db`
   * - If omitted: Uses ephemeral in-memory database
   */
  id?: string;
}

// Persistent storage
const agent = await AgentFS.open({ id: 'my-agent' });
// Creates: .agentfs/my-agent.db

// Ephemeral in-memory database
const ephemeralAgent = await AgentFS.open();

```

#### Properties

- `kv`: Key-value store interface
- `fs`: Filesystem interface
- `tools`: Tool call tracking interface
- `db`: Direct access handle for the underlying Turso database

### Key-Value Store API

 Fast typed storage for agent configuration and state.

#### `kv.set(key: string, value: any)`

 Save a value and JSON-serialize it automatically.

```
await agent.kv.set('user:123', {
  name: 'Alice',
  preferences: { theme: 'dark' }
});

await agent.kv.set('session:current', 'abc-123');
await agent.kv.set('counter', 42);

```

#### `kv.get<T>(key: string): Promise<T | undefined>`

 Load a value and automatically deserialize it.

```
interface UserData {
  name: string;
  preferences: { theme: string };
}

const user = await agent.kv.get<UserData>('user:123');
if (user) {
  console.log(user.name); // Type-safe access
}

```

#### `kv.delete(key: string)`

 Remove a key-value pair.

```
await agent.kv.delete('session:expired');

```

#### `kv.list(prefix: string): Promise<{ key: string, value: any }[]>`

 List keys matching a prefix.

```
// List all user keys
const userEntries = await agent.kv.list('user:');
for (const { key, value } of userEntries) {
  console.log(key, value);
}

// List all keys (empty prefix)
const allEntries = await agent.kv.list('');

```

### Filesystem API

 POSIX-style filesystem operations used to manage agent data.

#### `fs.writeFile(path: string, data: string | Buffer)`

 Write data into a file and create missing parent directories when necessary.

```
// Write text
await agent.fs.writeFile('/reports/summary.md', '# Report\nContent...');

// Write binary data
const imageBuffer = await fetch(url).then(r => r.arrayBuffer());
await agent.fs.writeFile('/images/chart.png', Buffer.from(imageBuffer));

```

#### `fs.readFile(path: string, options?: BufferEncoding): Promise<Buffer | string>`

 Read the contents of a file. By default this returns a Buffer; specify an encoding to receive a string.

```
// Read as Buffer (default)
const data = await agent.fs.readFile('/reports/summary.md');
const text = data.toString('utf-8');

// Read as string with encoding
const content = await agent.fs.readFile('/reports/summary.md', 'utf-8');

// For JSON files
const jsonData = await agent.fs.readFile('/config.json');
const config = JSON.parse(jsonData.toString());

```

#### `fs.readdir(path: string): Promise<string[]>`

 List directory contents.

```
const files = await agent.fs.readdir('/reports');
// Returns: ['summary.md', '2024/', 'archive/']

// Check each entry type
for (const entry of files) {
  const stats = await agent.fs.stat(`/reports/${entry}`);
  if (stats.isDirectory()) {
    console.log(`Directory: ${entry}`);
  } else {
    console.log(`File: ${entry} (${stats.size} bytes)`);
  }
}

```

#### `fs.stat(path: string): Promise<Stats>`

 Get file or directory metadata.

```
interface Stats {
  size: number;        // File size in bytes
  mode: number;        // File mode/permissions
  mtime: number;       // Modified time (Unix timestamp)
  ctime: number;       // Created time (Unix timestamp)
  isFile(): boolean;
  isDirectory(): boolean;
}

const stats = await agent.fs.stat('/reports/summary.md');
console.log(`Size: ${stats.size} bytes`);
console.log(`Modified: ${new Date(stats.mtime * 1000)}`);

```

#### `fs.exists(path: string): Promise<boolean>`

 Determine whether a file or directory is present.

```
if (await agent.fs.exists('/reports/draft.md')) {
  console.log('Draft exists');
}

```

#### `fs.deleteFile(path: string)`

 Delete a file.

```
await agent.fs.deleteFile('/reports/old.md');

```

### Tool Call Tracking API

 Store and query agent tool invocations for compliance and debugging workflows.

#### `tools.record(name, started_at, completed_at, parameters?, result?, error?): Promise<number>`

 Record a tool invocation.

```
await agent.tools.record(
  name: string,           // Tool identifier
  started_at: number,     // Unix timestamp (seconds)
  completed_at: number,   // Unix timestamp (seconds)
  parameters?: any,       // Tool parameters (JSON-serializable)
  result?: any,           // Tool result (JSON-serializable)
  error?: string          // Error message if failed
);

// Example: Track an API call
const start = Date.now() / 1000;
const response = await callOpenAI(prompt);

const id = await agent.tools.record(
  'openai_completion',
  start,
  Date.now() / 1000,
  { prompt, model: 'gpt-4', temperature: 0.7 },
  { response, tokensUsed: 150 }
);

```

#### `tools.get(id: number): Promise<ToolCall | undefined>`

 Fetch information for one specific tool call.

```
const toolCall = await agent.tools.get(42);
if (toolCall) {
  console.log('Duration:', toolCall.duration_ms, 'ms');
}

```

### Browser Support

 AgentFS can run in browser environments through WebAssembly:

```
<!DOCTYPE html>
<html>
<head>
  <script type="module">
    import { AgentFS } from 'https://unpkg.com/agentfs-sdk/dist/browser.js';

    const agent = await AgentFS.open({ id: 'browser-agent' });

    // All APIs work the same in browser
    await agent.kv.set('browser:data', { platform: 'web' });
    await agent.fs.writeFile('/notes.txt', 'Hello from browser!');
  </script>
</head>
</html>

```

### Support

- [GitHub Issues](https://github.com/tursodatabase/agentfs/issues) - Send bug reports or feature requests
- [Discord](https://tur.so/discord) - Community help and discussion
- [Examples](https://github.com/tursodatabase/agentfs/tree/main/examples) - Sample applications
