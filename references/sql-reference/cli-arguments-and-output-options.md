# CLI arguments and output options

[Context index](shell-and-compatibility-workflows.md) · [Continue](cli-filesystems-readonly-and-server-modes.md)

## Command-Line Options

```
tursodb [OPTIONS] [DATABASE] [SQL]

```

### Arguments

| Argument | Default | Description |
| --- | --- | --- |
| `DATABASE` | `:memory:` | SQLite database file path. Without a value, the shell uses a temporary in-memory database |
| `SQL` | — | SQL statement for execution. When supplied, the shell executes that query and then exits |

### Examples

```
# In-memory database
tursodb

# Open an existing file
tursodb customers.db

# Run a query and exit
tursodb customers.db "SELECT count(*) FROM orders;"

# Pipe SQL from stdin
echo "SELECT 1 + 1;" | tursodb -q

```

### Options

### `-m, --output-mode <MODE>`

 Set the output display format.

| Mode | Description |
| --- | --- |
| `pretty` | Table with borders (default) |
| `list` | Pipe-delimited values |
| `line` | Column names are shown, with each column placed on its own line |

```
tursodb -m list mydata.db "SELECT * FROM users;"

```

### `-o, --output <PATH>`

 Send query output to a file rather than stdout.

```
tursodb -o results.txt mydata.db "SELECT * FROM users;"

```

### `-q, --quiet`

 Hide the startup banner and version details.

```
tursodb -q mydata.db

```
