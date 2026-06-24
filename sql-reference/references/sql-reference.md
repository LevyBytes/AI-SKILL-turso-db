# Cli, Compatibility

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Command-Line Options; Getting Started; Shell Commands; SQLite Compatibility.

## Command-Line Options

### Command-Line Options

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

### Experimental Feature Flags

 These flags turn on work-in-progress features. Future releases may revise or remove them. Implementations can be incomplete or carry known limits, so reserve them for testing and development.
| Flag | Description |
| --- | --- |
| `--experimental-views` | Enable views (`CREATE VIEW` / `DROP VIEW`) |
| `--experimental-custom-types` | Enable custom types (`CREATE TYPE` / `DROP TYPE`) |
| `--experimental-encryption` | Enable at-rest database encryption |
| `--experimental-index-method` | Turn on custom index methods. Required for FTS and Sparse Vector indexes |
| `--experimental-autovacuum` | Enable automatic database vacuuming |
| `--experimental-vacuum` | Enable in-place `VACUUM` |
| `--experimental-triggers` | Enable triggers (`CREATE TRIGGER` / `DROP TRIGGER`) |
| `--experimental-attach` | Enable `ATTACH DATABASE` / `DETACH DATABASE` |

```
# Enable views, triggers, and in-place VACUUM
tursodb --experimental-views --experimental-triggers --experimental-vacuum mydata.db

```

### Other Flags

| Flag | Description |
| --- | --- |
| `--unsafe-testing` | Enable unsafe testing features (e.g., `sqlite_dbpage` writes) |
| `-h, --help` | Print usage help |
| `-V, --version` | Print version information |

## Getting Started

### Getting Started

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

### Sync Server

 Launch a sync server for replication:

```
tursodb --sync-server 0.0.0.0:8080 mydata.db

```

## Shell Commands

### Shell Commands

 Dot commands are interactive-shell commands prefixed by a period (`.`). They can be entered without a trailing semicolon.

```
tursodb> .help

```

### Database & Navigation

### .open

 Open a database file and, if needed, name a VFS backend.

```
.open <PATH> [VFS]

```

```
tursodb> .open mydata.db
tursodb> .open mydata.db memory

```

### .quit

 Leave the shell. Aliases: `.q`, `.qu`, `.qui`.

```
tursodb> .quit

```

### .exit

 Leave the shell, optionally with a return code. Aliases: `.ex`, `.exi`.

```
.exit [CODE]

```

```
tursodb> .exit
tursodb> .exit 1

```

### .cd

 Change the current working directory.

```
.cd <DIRECTORY>

```

```
tursodb> .cd /tmp

```

### Schema Inspection

### .tables

 Show every database table, with an optional pattern filter.

```
.tables [PATTERN]

```

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE departments (id INTEGER PRIMARY KEY, name TEXT);

```

```
tursodb> .tables
employees
departments

tursodb> .tables emp%
employees

```

### .schema

 Show the CREATE statement for one table, or for all tables when no argument is supplied.

```
.schema [TABLE]

```

```
tursodb> .schema employees
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);

tursodb> .schema
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE departments (id INTEGER PRIMARY KEY, name TEXT);

```

### .indexes

 Display indexes by name, optionally limited to one table.

```
.indexes [TABLE]

```

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT, department TEXT);
CREATE INDEX idx_dept ON employees(department);
CREATE INDEX idx_name ON employees(name);

```

```
tursodb> .indexes
idx_dept
idx_name

tursodb> .indexes employees
idx_dept
idx_name

```

### .databases

 List all attached databases.

```
tursodb> .databases
main: /path/to/mydata.db r/w

```

### Output Control

### .mode

 Set the output display mode.

```
.mode <MODE>

```

| Mode | Description |
| --- | --- |
| `pretty` | Table with borders (default) |
| `list` | Pipe-delimited values |
| `line` | Column names are included, with one column shown on each line |

```
tursodb> .mode list
tursodb> SELECT 1 AS a, 2 AS b;
1|2

tursodb> .mode line
tursodb> SELECT 1 AS a, 2 AS b;
a = 1
b = 2

tursodb> .mode pretty
tursodb> SELECT 1 AS a, 2 AS b;
┌───┬───┐
│ a │ b │
├───┼───┤
│ 1 │ 2 │
└───┴───┘

```

### .headers

 Switch column headers on or off for `list` mode.

```
.headers <on|off>

```

```
tursodb> .mode list
tursodb> .headers on
tursodb> SELECT 1 AS x, 2 AS y;
x|y
1|2

tursodb> .headers off
tursodb> SELECT 1 AS x, 2 AS y;
1|2

```

### .nullvalue

 Choose the text shown for NULL values in `list` mode.

```
.nullvalue <STRING>

```

```
tursodb> .mode list
tursodb> .nullvalue [NULL]
tursodb> SELECT 1 AS a, NULL AS b;
1|[NULL]

```

### .output

 Send query output to a file. Use no argument, or pass `stdout`, to return output to the terminal.

```
.output [PATH]

```

```
tursodb> .output results.txt
tursodb> SELECT * FROM employees;
tursodb> .output
tursodb> -- Output is back to the terminal

```

### .echo

 Switch echo mode. When enabled, every SQL statement is printed before it runs.

```
.echo <on|off>

```

```
tursodb> .echo on
tursodb> SELECT 42;
SELECT 42;
┌────┐
│ 42 │
├────┤
│ 42 │
└────┘

```

### .show

 Display current shell settings.

```
tursodb> .show
Settings:
Output mode: pretty
DB: mydata.db
Output: STDOUT
Null value:
CWD: /home/user
Echo: off
Headers: off

```

### Performance & Diagnostics

### .timer

 Switch query timing. When enabled, execution time and I/O statistics appear after each query.

```
.timer <on|off>

```

```
tursodb> .timer on
tursodb> SELECT 42;
42
Command stats:
----------------------------
total: 442 us (this includes parsing/coloring of cli app)

query execution stats:
----------------------------
Execution: avg=4 us, total=8 us
I/O: No samples available

```

### .stats

 Show database statistics. `on`/`off` controls whether they appear after every query; `--reset` clears counters.

```
.stats [on|off] [--reset]

```

```
tursodb> .stats
Connection Metrics:
  Total statements:     3
  High-water marks:
    Max VM steps:       19
    Max rows read:      1

Aggregate Statistics:
Statement Metrics:
  Row Operations:
    Rows read:        1
    Rows written:     2
  Execution:
    VM steps:         48
    Instructions:     47
  ...

```

### .opcodes

 Display VDBE (Virtual Database Engine) opcodes and their descriptions. Filtering by opcode name is optional.

```
.opcodes [OPCODE]

```

```
tursodb> .opcodes Integer
Integer
-------
The 64-bit integer value P1 is written into register P2. This is different
from SQLite, where this opcode is used for 32-bit integers

```

### .vfslist

 List available Virtual File System modules.

```
tursodb> .vfslist
Available VFS modules:
memory
syscall

```

### Data Import & Export

### .dump

 Emit SQL statements for the full database so it can be recreated.

```
tursodb> .dump
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO "employees" VALUES(1,'Alice');
INSERT INTO "employees" VALUES(2,'Bob');
COMMIT;

```

 Pipe `.dump` output into another Turso instance to rebuild the database:

```
tursodb original.db ".dump" | tursodb -q clone.db

```

### .import

 Load data from a file into a table.

```
.import [--csv] [--skip N] [--verbose] <FILE> <TABLE>

```

| Option | Default | Description |
| --- | --- | --- |
| `--csv` | on | Set comma as the field separator and newline as the record separator |
| `--skip N` | 0 | Omit the first N rows, which is useful for header rows |
| `--verbose` | off | Print progress information during import |

```
tursodb> CREATE TABLE people (name TEXT, age INTEGER);
tursodb> .import --csv --skip 1 data.csv people
tursodb> SELECT * FROM people;
┌─────────┬─────┐
│ name    │ age │
├─────────┼─────┤
│ Alice   │  30 │
├─────────┼─────┤
│ Bob     │  25 │
├─────────┼─────┤
│ Charlie │  35 │
└─────────┴─────┘

```

 For a CSV file named `data.csv`:

```
name,age
Alice,30
Bob,25
Charlie,35

```

### .clone

 Copy the current database into a new file.

```
.clone <OUTPUT_FILE>

```

```
tursodb> .clone backup.db
employees... done

```

### .read

 Run SQL statements read from a file.

```
.read <PATH>

```

```
tursodb> .read setup.sql

```

### .load

 Load an extension library.

```
.load <PATH>

```

```
tursodb> .load ./liblimbo_regexp

```

 Turso can load only Turso-native extensions. SQLite `.so`/`.dll` loadable extensions do not work. Available extensions are listed in the [Extensions](https://docs.turso.tech/sql-reference/extensions) documentation.

### Debugging

### .dbtotxt

 Show raw database pages in hex format. Helpful when investigating storage-level problems.

```
.dbtotxt [--page PAGE_NO]

```

```
tursodb> .dbtotxt --page 1
| size 8192 pagesize 4096 filename :memory:
| page 1 offset 0
|      0: 53 51 4c 69 74 65 20 66 6f 72 6d 61 74 20 33 00   SQLite format 3.
|     16: 10 00 02 02 00 40 20 20 00 00 00 01 00 00 00 02   .....@  ........
...

```

### .dbconfig

 Show or change database configuration flags. In current Turso builds, this is a no-op.

```
.dbconfig [CONFIG] [on|off]

```

### Documentation

### .manual

 Show Turso built-in manual pages. With no argument, it lists all available manuals.

```
.manual [PAGE]

```

 Aliases: `.man`.

```
tursodb> .manual
# Turso Manual Pages

Available manuals:
  custom-types    Custom Types for STRICT Tables
  cdc             Change Data Capture
  encryption      At-Rest Database Encryption
  vector          Vector Search
  materialized-views  Live Materialized Views
  mcp             Model Context Protocol server

tursodb> .manual vector

```

## SQLite Compatibility

### SQLite Compatibility

 The main compatibility reference lives at [`COMPAT.md`](https://github.com/tursodatabase/turso/blob/main/COMPAT.md) in the repository root. It records support status for each SQL statement, expression, function, PRAGMA, C API function, VDBE opcode, and extension. Use that file to check the current compatibility state. Code changes update it so the status remains accurate.

### Turso-Specific Extensions

 Turso adds these capabilities beyond SQLite compatibility:
| Feature | Description |
| --- | --- |
| CREATE TYPE | STRICT tables can use user-defined types |
| CREATE MATERIALIZED VIEW | Incrementally maintained live materialized views |
| BEGIN CONCURRENT | Optimistic concurrent write transactions (MVCC) |
| Vector functions | Vector storage, distance calculations, similarity search |
| Full-text search | FTS backed by Tantivy with BM25 scoring |
| CDC | Change Data Capture via PRAGMA |
| Encryption | At-rest database encryption |
| Custom index methods | FTS and custom access methods can use CREATE INDEX … USING |
| stddev() | Standard deviation aggregate function |
| DEFAULT in VALUES | INSERT VALUES lists support the SQL-standard DEFAULT keyword |

### See Also

- [Experimental Features](https://docs.turso.tech/sql-reference/experimental-features) explains enabling experimental features
- [COMPAT.md](https://github.com/tursodatabase/turso/blob/main/COMPAT.md) contains the full compatibility matrix
