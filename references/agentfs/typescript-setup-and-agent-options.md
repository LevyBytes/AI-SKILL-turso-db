# TypeScript setup and agent options

[Context index](agentfs-language-sdks.md) · [Continue](typescript-key-value-and-file-writing.md)

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
const now = Date.now() / 1000;
await agent.tools.record('write_file', now, now,
  { path: '/file.txt' }, { bytes: 4 });

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
