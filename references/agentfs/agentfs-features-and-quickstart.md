# AgentFS features and quickstart

[Context index](installation-and-cli-workflows.md)

## Key Features

AgentFS gives AI agents a filesystem layer. Its copy-on-write model lets an agent change files in isolation while the source data remains unchanged. You can use it as a CLI that launches an existing program inside a sandboxed session, or as an SDK for building custom agents around a filesystem abstraction.

### Key Features

- **Copy-on-Write Isolation** - Run agents inside sandboxed environments so edits stay separate from your source tree
- **Single File Storage** - Store the full state in one portable SQLite database for simpler sharing and snapshot creation
- **Built-in Auditing** - Record every file operation so it can be queried later
- **Cloud Sync** - Sync agent state to Turso Cloud when that option is enabled

### Quick Start

```
# Install
curl -fsSL https://agentfs.ai/install | bash

# Run a shell in an isolated environment
cd /path/to/project
agentfs run bash

# Any changes are isolated - your original files are safe

```

### Next Steps

### Installation

Install the AgentFS CLI

### Agentic Coding

Run AI coding agents safely

### AgentFS SDKs

Use the AgentFS SDKs to build custom agents. This software is in BETA and may still have bugs or behave unexpectedly, so treat production data carefully and keep backups available.
