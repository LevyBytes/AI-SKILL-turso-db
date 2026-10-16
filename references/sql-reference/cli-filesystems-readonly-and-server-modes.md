# CLI filesystems readonly and server modes

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](cli-arguments-and-output-options.md) · [Continue](cli-experimental-and-other-flags.md)

### `-e, --echo`

 Show every SQL statement immediately before execution. Helpful while diagnosing scripts.

```
echo "SELECT 42;" | tursodb -q -e

```

```
SELECT 42;
42

```

### `-v, --vfs <VFS>`

 Choose the Virtual File System backend.

| VFS | Description |
| --- | --- |
| `syscall` | Standard OS file I/O (default) |
| `memory` | In-memory storage |
| `io_uring` | Linux io_uring (requires feature flag) |

```
tursodb -v memory

```

### `-t, --tracing-output <PATH>`

 Store internal trace logs in a file. Helpful for debugging and performance review.

```
tursodb -t trace.log mydata.db

```

### `--readonly`

 Use read-only mode when opening the database. Any write attempt returns an error.

```
tursodb --readonly production.db "SELECT count(*) FROM users;"

```

 Example of a write attempt while in read-only mode:

```
tursodb --readonly production.db "INSERT INTO users VALUES (1, 'test');"
# Error: Resource is read-only

```

### `--mcp`

 Run a Model Context Protocol server, not the interactive shell. AI assistants and other MCP clients can then access the database through JSON-RPC.

```
tursodb --mcp mydata.db

```

### `--sync-server <ADDRESS>`

 Launch a sync server that listens on the supplied address for database replication.

```
tursodb --sync-server 0.0.0.0:8080 mydata.db

```
