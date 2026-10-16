# Shell output and MCP server

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-startup-and-query-input.md) · [Continue](shell-sync-server.md)

### Pretty (Default)

 Human-readable table with borders:

```
tursodb -q -m pretty

```

```
┌────┬─────────┬─────────────┬─────────┐
│ id │ name    │ department  │ salary  │
├────┼─────────┼─────────────┼─────────┤
│  1 │ Alice   │ Engineering │ 95000.0 │
├────┼─────────┼─────────────┼─────────┤
│  2 │ Bob     │ Marketing   │ 72000.0 │
└────┴─────────┴─────────────┴─────────┘

```

### List

 Pipe-delimited output for scripting:

```
tursodb -q -m list

```

```
1|Alice|Engineering|95000.0
2|Bob|Marketing|72000.0

```

### Line

 Columns appear one per line, labeled with their names:

```
tursodb -q -m line

```

```
        id = 1
      name = Alice
department = Engineering
    salary = 95000.0

```

### Non-Interactive Mode

 With piped input rather than a terminal, the shell switches to non-interactive mode:
- Defaults output to `list` mode (change with `-m`)
- Omits the startup banner, history, and syntax highlighting
- Returns exit code 1 when a query fails

```
echo "SELECT 1 + 2;" | tursodb -q

```

### Server Modes

 The CLI also supports server operation in place of the interactive shell.

### MCP Server

 Run a [Model Context Protocol](https://modelcontextprotocol.io/) server so AI assistants can access the database:

```
tursodb --mcp mydata.db

```
