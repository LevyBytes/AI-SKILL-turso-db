# Shell startup and query input

[Context index](shell-and-compatibility-workflows.md) · [Continue](shell-output-and-mcp-server.md)

## Getting Started

 The Turso CLI (`tursodb`) provides an interactive SQL shell for Turso databases. It works with in-memory and file-backed databases and includes output modes, CSV import, cloning, and built-in docs.

### Quick Start

### In-Memory Database

 Start the shell against a temporary in-memory database:

```
tursodb

```

### Open a Database File

```
tursodb mydata.db

```

### Run a Query Directly

 Provide SQL on the command line to execute it and quit:

```
tursodb mydata.db "SELECT * FROM users;"

```

### Pipe SQL from stdin

```
echo "SELECT sqlite_version();" | tursodb -q

```

### Interactive Shell

 Without a SQL argument, startup enters an interactive REPL with history and syntax highlighting.

### Multi-line Input

 Incomplete statements, such as missing semicolons or unbalanced parentheses, keep accepting input on the following line. The prompt shows the current nesting depth:

```
tursodb> SELECT *
   ...>   FROM employees
   ...>   WHERE department = 'Engineering';

```

### Command History

 The shell writes command history to `~/.limbo_history` automatically, and the up/down arrow keys can recall it.

### Exiting

 Leave the shell with `.quit` or `.exit`. Press Ctrl+C twice for another exit path.

### Output Modes

 Turso offers three output modes, chosen with `-m` or the `.mode` dot command.
