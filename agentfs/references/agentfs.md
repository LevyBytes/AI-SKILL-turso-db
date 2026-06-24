# Guides, Installation

> Agentfs reference. Original prose; identifiers preserved verbatim.

## Overview

This reference covers timeline inspection, MCP and NFS server startup, overlay behavior, supported agent workflows, session creation, sync setup, and installation.

## Viewing the Timeline

AgentFS logs each file operation and tool call, so agent activity can be inspected end to end. That record supports debugging, compliance review, and analysis of how agents behave.

### Viewing the Timeline

 Show tool calls in time order:

```
agentfs timeline my-session

```

 Output:

```
ID   TOOL                 STATUS       DURATION STARTED
4    execute_code         pending            -- 2024-01-05 09:44:20
3    api_call             error           300ms 2024-01-05 09:44:15
2    read_file            success          50ms 2024-01-05 09:44:10
1    web_search           success        1200ms 2024-01-05 09:43:45

```

### Filtering Results

 Show only specific tool types:

```
agentfs timeline my-session --filter web_search

```

 Show only errors:

```
agentfs timeline my-session --status error

```

 Limit number of entries:

```
agentfs timeline my-session --limit 20

```

### JSON Output

 For programmatic analysis:

```
agentfs timeline my-session --format json

```

```
[
  {
    "id": 1,
    "name": "web_search",
    "status": "success",
    "started_at": 1704447825,
    "completed_at": 1704447826,
    "duration_ms": 1200,
    "parameters": {"query": "AI agents"},
    "result": {"results": ["result1", "result2"]}
  }
]

```

### Inspecting Files

 Show every file in a session:

```
agentfs fs ls my-session

```

 Output:

```
d artifacts
f config.json
f output.txt

```

 List a subdirectory:

```
agentfs fs ls my-session /artifacts

```

 Read file contents:

```
agentfs fs cat my-session /output.txt

```

### Viewing Changes (Diff)

 Display the differences from the original filesystem:

```
agentfs diff my-session

```

 This shows:
- New files created
- Modified files
- Deleted files

### Querying with SQL

 Because AgentFS is backed by SQLite, arbitrary SQL queries are supported:
-  Turso
-  SQLite

```
tursodb .agentfs/my-session.db

```

```
sqlite3 .agentfs/my-session.db

```

 Example queries:

```
-- Find all tool calls that took longer than 1 second
SELECT name, duration_ms
FROM toolcalls
WHERE duration_ms > 1000;

-- Count tool usage by type
SELECT name, COUNT(*) as count
FROM toolcalls
GROUP BY name
ORDER BY count DESC;

-- Find files modified in the last hour
SELECT path, mtime
FROM fs_inode
WHERE mtime > strftime('%s', 'now', '-1 hour');

-- Get total bytes written
SELECT SUM(size) as total_bytes
FROM fs_inode
WHERE mode & 0170000 = 0100000;  -- regular files only

```

### Use Cases

### Debugging Agent Failures

 If an agent fails, review the timeline to reconstruct the sequence of events:

```
# See recent activity
agentfs timeline my-session --limit 20

# Focus on errors
agentfs timeline my-session --status error

# Check what files were created/modified
agentfs fs ls my-session

```

### Performance Analysis

 Find slow operations:

```
SELECT name, AVG(duration_ms) as avg_ms, COUNT(*) as count
FROM toolcalls
GROUP BY name
ORDER BY avg_ms DESC;

```

### Compliance Auditing

 Produce a full export of agent activity:

```
agentfs timeline my-session --format json > audit-log.json

```

### Next Steps

### Overlay Filesystem

Understanding copy-on-write

### Syncing

Sync sessions to Turso Cloud

## Starting the MCP Server

AgentFS can operate as an [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) server, making filesystem and key-value operations available to AI assistants such as Claude Desktop.

### Starting the MCP Server

```
agentfs serve mcp my-agent

```

 This launches an MCP server and publishes AgentFS tools through stdio.

### Available Tools

### Filesystem Tools

| Tool | Description |
| --- | --- |
| `read_file` | Read file contents |
| `write_file` | Write file contents |
| `readdir` | List directory contents |
| `mkdir` | Create directory |
| `remove` | Remove file or directory |
| `rename` | Rename file or directory |
| `stat` | Get file metadata |
| `access` | Check file access permissions |

### Key-Value Tools

| Tool | Description |
| --- | --- |
| `kv_get` | Get value by key |
| `kv_set` | Set key-value pair |
| `kv_delete` | Delete key |
| `kv_list` | List all keys |

### Limiting Exposed Tools

 For a tighter security boundary, restrict the tools that are exposed:

```
# Read-only mode
agentfs serve mcp my-agent --tools read_file,readdir,stat,kv_get,kv_list

# Filesystem only
agentfs serve mcp my-agent --tools read_file,write_file,readdir,mkdir,remove

# Key-value only
agentfs serve mcp my-agent --tools kv_get,kv_set,kv_delete,kv_list

```

### Security Considerations

 The MCP server grants AI assistants direct access to the AgentFS filesystem. Apply these security practices:
- **Restrict tools** - Expose only the tools the assistant actually requires
- **Use overlays** - Prefer overlay filesystems over direct access to real directories
- **Inspect the agent database** - Review `.agentfs/*.db` on a regular basis

### Debugging

 Verify that the MCP server is responding:

```
# Run manually to see output
agentfs serve mcp my-agent

# Check if the database exists
ls -la .agentfs/my-agent.db

```

### Next Steps

### NFS Server

Remote access via NFS

### Copy-on-Write Overlays

How isolation works

## Starting the NFS Server

AgentFS can serve filesystems through NFS, allowing other machines, containers, or virtual machines to access them remotely.

### Starting the NFS Server

```
agentfs serve nfs my-agent

```

 By default, this listens on `127.0.0.1:11111`.

### Binding to All Interfaces

 To allow remote access:

```
agentfs serve nfs my-agent --bind 0.0.0.0 --port 2049

```

 Binding NFS to `0.0.0.0` exposes the filesystem to any host that can connect to your machine. Use firewall rules or a VPN in production.

### Mounting the Filesystem

### From the Same Machine

```
mkdir /mnt/agentfs
mount -t nfs -o vers=3,tcp,port=11111,mountport=11111,nolock 127.0.0.1:/ /mnt/agentfs

```

### From a Remote Machine

```
mount -t nfs -o vers=3,tcp,port=2049,mountport=2049,nolock server-ip:/ /mnt/agentfs

```

### Mount Options Explained

| Option | Description |
| --- | --- |
| `vers=3` | Use NFSv3 protocol |
| `tcp` | Use TCP transport |
| `port=11111` | NFS server port |
| `mountport=11111` | Mount protocol port |
| `nolock` | Disable file locking (single-user) |

### Unmounting

```
umount /mnt/agentfs

```

 If the mount is busy:

```
umount -f /mnt/agentfs  # Force unmount
# or
umount -l /mnt/agentfs  # Lazy unmount

```

### Next Steps

### MCP Server

Expose AgentFS via MCP

### Sync with Turso Cloud

Persist and share agent state

## How Overlay Works

AgentFS provides copy-on-write isolation through an overlay filesystem, allowing agents to modify files without altering the original data.

### How Overlay Works

 An overlay filesystem is made from two layers:

```
┌────────────────────────────────────┐
│         Merged View (what you see) │
├────────────────────────────────────┤
│  Delta Layer (AgentFS database)    │  ← Writes go here
├────────────────────────────────────┤
│  Base Layer (original directory)   │  ← Read-only
└────────────────────────────────────┘

```

 **Reading a file:**
1. Check delta layer first
2. If the file is absent there, read it from the base layer
 **Writing a file:**
1. Copy the file into the delta layer (when it originated in the base layer)
2. Write changes to delta layer
3. Base layer is never modified
 **Deleting a file:**
1. Add a "whiteout" marker to the delta layer
2. The merged view treats the file as deleted
3. The original file in the base layer is preserved

### Creating an Overlay Filesystem

### With `agentfs run`

 The most direct approach automatically places an overlay on top of your current directory:

```
cd /path/to/project
agentfs run /bin/bash

```

### With `agentfs init --base`

 Create an overlay explicitly:

```
agentfs init my-overlay --base /path/to/project

```

 Then mount it:

```
agentfs mount my-overlay ./workspace

```

 You can also execute a command inside it:

```
agentfs run --session my-overlay /bin/bash

```

### Viewing Changes

 Display differences from the base:

```
agentfs diff my-overlay

```

### Practical Examples

### Safe Refactoring

 Have an agent refactor code while keeping the source tree protected:

```
cd my-project
agentfs run --session refactor python3 refactor_agent.py

# Review what changed
agentfs diff refactor

# Happy? Apply changes manually or via script
# Not happy? Just delete the session
rm .agentfs/refactor.db

```

### Testing Destructive Operations

 Try something dangerous safely:

```
agentfs run --session test /bin/bash
$ rm -rf src/  # Yikes!
$ exit

# Original files are fine
ls src/  # Still there!

# Only the overlay was affected
agentfs fs ls test  # Shows the deletion

```

### Parallel Experiments

 Try several experiments against the same codebase:

```
# Experiment A
agentfs run --session exp-a python3 approach_a.py

# Experiment B (same base, different changes)
agentfs run --session exp-b python3 approach_b.py

# Compare results
agentfs diff exp-a
agentfs diff exp-b

```

### Storage Efficiency

 The overlay only stores:
- **Changed files** - A full copy is stored after the first write
- **New files** - The full file lives in delta
- **Deleted files** - Small whiteout marker
 Original files are not copied. In a 10GB project, 1MB of edits consumes only ~1MB in the delta layer.

### Technical Details

### File Operations

| Operation | Behavior |
| --- | --- |
| Read existing file | Pass through to base |
| Write existing file | Copy to delta, then write |
| Create new file | Write directly to delta |
| Delete file | Create whiteout in delta |
| Rename file | Delete old + create new |

### Whiteouts

 Deleting a base file causes AgentFS to write a "whiteout", which is a marker that masks the file:

```
-- Whiteouts in the database
SELECT * FROM fs_inode WHERE whiteout = 1;

```

### Database Schema

 The delta layer is persisted in SQLite:

```
-- Files and directories
CREATE TABLE fs_inode (
    ino INTEGER PRIMARY KEY,
    parent_ino INTEGER,
    name TEXT,
    mode INTEGER,
    size INTEGER,
    ...
    whiteout INTEGER DEFAULT 0
);

-- File contents
CREATE TABLE fs_block (
    ino INTEGER,
    block_num INTEGER,
    data BLOB
);

```

 The full schema is documented in the [AgentFS Specification](https://github.com/tursodatabase/agentfs/blob/main/SPEC.md).

### Next Steps

### Sessions

Share state between agents

### Syncing

Sync overlays to Turso Cloud

## Supported Agents

AgentFS runs AI coding agents such as Claude Code, Codex, and OpenCode in a sandbox where file edits stay separate from the original source tree.

### Supported Agents

| Agent | Command |
| --- | --- |
| Claude Code | `agentfs run claude` |
| Codex | `agentfs run codex` |
| OpenCode | `agentfs run opencode` |
| Any CLI tool | `agentfs run <command>` |

### Workflow

 A typical agentic coding flow looks like this. The example uses Claude Code, and the same pattern applies to OpenCode, Codex, and other agents.

### Step 1: Clone the Repository

 Clone the git repository into a host directory and keep that source tree free of build artifacts. After main is updated, pull into this repository so it stays current.

```
git clone https://github.com/tursodatabase/agentfs.git
cd agentfs

```

### Step 2: Start an AgentFS Session

 Start a session with `agentfs run` and the `--session` flag. Choose a name that can later be reused as your git branch name.
-  Claude Code
-  Codex
-  OpenCode

```
agentfs run --session fix-bug-123 claude

```

```
agentfs run --session fix-bug-123 codex

```

```
agentfs run --session fix-bug-123 opencode

```

 The agent is now working inside a copy-on-write environment, leaving the original source tree unchanged.

### Step 3 (Optional): Manual Investigation

 When the agent behaves unexpectedly, use a second terminal to attach to the same session for inspection:

```
agentfs run --session fix-bug-123 bash

```

 The shell and the agent see the same filesystem. You can browse files, run tests, and observe the current state while the agent continues working.

### Step 4: Push to Branch

 After the agent finishes, tell it to create a branch, commit the code, and push:

>  “Create a branch called fix-bug-123, commit these changes, and push to origin”

 From there, open a PR manually from the pushed branch.

### Why This Workflow?

- **Clean source tree** - The original clone remains clean and simple to update from upstream
- **Separated changes** - Every task has its own session, avoiding change leakage between tasks
- **Simple inspection** - Attach to any session to view activity in real time
- **Standard git flow** - Push branches and open PRs using the normal process

### Next Steps

### Multi-Agent Sessions

Use shared state across agents and terminals

### Auditing

Review agent activity with diff and timeline

## Creating a Session

Named sessions allow multiple agents, terminals, or runs to share one filesystem. Everyone attached sees the same copy-on-write project view.

### Creating a Session

 Create a named session with `--session`:

```
agentfs run --session my-project /bin/bash

```

 This creates the `my-project` session, with changes stored in `.agentfs/my-project.db`.

### Resuming a Session

 Run the command again to continue from the previous state:

```
# First run - make some changes
agentfs run --session my-project /bin/bash
$ echo "hello" > test.txt
$ exit

# Second run - changes are still there
agentfs run --session my-project /bin/bash
$ cat test.txt
hello

```

### Multi-Terminal Collaboration

 More than one terminal can connect to the same session at the same time:

```
# Terminal 1
agentfs run --session shared-work /bin/bash

# Terminal 2 - joins the same filesystem
agentfs run --session shared-work python3 agent.py

# Terminal 3 - also sees the same files
agentfs run --session shared-work vim

```

 Every terminal uses the same copy-on-write filesystem, so edits from one terminal appear immediately in the others.

### Use Cases

### Iterative Development

 Continue work on a task through multiple sessions:

```
# Morning session
agentfs run --session feature-x /bin/bash
# ... work on the feature, then exit for lunch

# Afternoon session - continue where you left off
agentfs run --session feature-x /bin/bash

```

### Safe Experimentation

 Start a session, attempt a risky change, and then choose whether to keep it:

```
agentfs run --session experiment /bin/bash
# ... try some changes

# Option A: Happy with changes
agentfs diff experiment  # Review
# Apply changes to real filesystem (manually or via script)

# Option B: Discard everything
rm .agentfs/experiment.db

```

### Agent Supervision

 Launch an agent and watch its progress from a separate terminal:

```
# Terminal 1: Run the agent
agentfs run --session agent-task python3 agent.py

# Terminal 2: Watch what it's doing
agentfs run --session agent-task /bin/bash
$ watch ls -la  # See files being created
$ tail -f agent.log  # Monitor logs

```

### Managing Sessions

 List files in a session:

```
agentfs fs ls my-session

```

 View session changes:

```
agentfs diff my-session

```

 Delete a session:

```
rm .agentfs/my-session.db

```

### Session Storage

 Session data is kept as SQLite databases under `.agentfs/`:

```
.agentfs/
├── my-project.db
├── experiment.db
└── shared-work.db

```

 Each database contains:
- All modified files
- Deleted file markers (whiteouts)
- Tool call audit log
- Key-value store data

### Next Steps

### Auditing

Review the events that occurred in a session

### Overlay Filesystem

How copy-on-write works

## Setting Up Sync

AgentFS can synchronize with [Turso Cloud](https://turso.tech), supporting backup, collaboration, and distributed agent deployments.

### Setting Up Sync

### 1. Create a Turso Database

 If you do not already have a Turso account, [sign up](https://turso.tech) first. Then create a database for the agent:

```
turso db create my-agent-db

```

 Get the database URL:

```
turso db show my-agent-db --url
# Output: libsql://my-agent-db-username.turso.io

```

### 2. Initialize with Sync

 Create a new synchronized agent filesystem:

```
agentfs init my-agent --sync-remote-url libsql://my-agent-db-username.turso.io

```

 Or enable sync for an agent that already exists:

```
# Edit the database to add sync configuration
# (Feature coming soon)

```

### Sync Commands

### Pull Remote Changes

 Download changes from Turso Cloud:

```
agentfs sync my-agent pull

```

### Push Local Changes

 Send local changes up to Turso Cloud:

```
agentfs sync my-agent push

```

### View Sync Status

 Check sync statistics:

```
agentfs sync my-agent stats

```

### Create Checkpoint

 Force a sync checkpoint:

```
agentfs sync my-agent checkpoint

```

### Use Cases

### Backup and Recovery

 Automatically back up agent state:

```
# After important work
agentfs sync my-agent push

# On a new machine
agentfs init my-agent --sync-remote-url libsql://...
agentfs sync my-agent pull

```

### Multi-Machine Agents

 Run agents on several machines while sharing state:

```
# Machine A
agentfs run --session shared-agent python3 agent.py
agentfs sync shared-agent push

# Machine B
agentfs sync shared-agent pull
agentfs run --session shared-agent python3 agent.py

```

### Partial Sync

 For large filesystems, partial sync lets you fetch only the required data:

```
agentfs init my-agent \
  --sync-remote-url libsql://... \
  --sync-partial-prefetch \
  --sync-partial-segment-size 1000

```

### Partial Sync Options

| Option | Description |
| --- | --- |
| `--sync-partial-prefetch` | Enable prefetching |
| `--sync-partial-segment-size` | Segment size for partial sync |
| `--sync-partial-bootstrap-query` | Custom bootstrap query |
| `--sync-partial-bootstrap-length` | Bootstrap prefix length |

### Authentication

 Sync needs a Turso auth token. Provide it with an environment variable:

```
export TURSO_AUTH_TOKEN=$(turso db tokens create my-agent-db)

```

 Alternatively, generate a long-lived token:

```
turso db tokens create my-agent-db --expiration none

```

### Conflict Resolution

 AgentFS resolves conflicts with last-write-wins. If multiple machines edit the same file:
1. Push from Machine A succeeds
2. Machine B cannot push because the operation conflicts
3. Machine B needs to pull first, then push

### Next Steps

### Turso Cloud

Learn more about Turso Cloud

### NFS Server

Remote access via NFS

## Quick Install

### Quick Install

 The quickest AgentFS installation path:

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

 This places the `agentfs` binary in `~/.cargo/bin`.

### Platform-Specific Instructions

-  macOS
-  Linux
-  Windows

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

Once installation finishes, restart the terminal or run:

```
source ~/.zshrc  # or ~/.bashrc

```

**Using the installer:**

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

Once installation finishes, restart the terminal or run:

```
source ~/.bashrc  # or ~/.zshrc

```

**Requirements:**
- FUSE needs to be present for filesystem mounting
- On Ubuntu/Debian: `sudo apt install fuse3`
- On Fedora: `sudo dnf install fuse3`
**Using PowerShell:**

```
irm https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.ps1 | iex

```

You can also get the binary directly from the [releases page](https://github.com/tursodatabase/agentfs/releases).

### Verify Installation

```
agentfs --version

```

### Shell Completions

 Turn on tab completion for your shell:

```
# Auto-detect shell
agentfs completions install

# Or specify shell explicitly
agentfs completions install bash
agentfs completions install zsh
agentfs completions install fish

```

 Restart the shell after the completions are installed.

### Building from Source

 Requires Rust 1.70+:

```
git clone https://github.com/tursodatabase/agentfs.git
cd agentfs/cli
cargo install --path .

```

### Next Steps

### Agentic Coding

Run AI coding agents safely

### CLI Reference

Full command reference
