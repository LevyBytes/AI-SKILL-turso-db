# Multiprocess Access, Pragmas

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Multi-Process Access; PRAGMA Statements.

## Multi-Process Access

### Multi-Process Access

 Multi-process access remains **experimental**. Turso may alter both the on-disk coordination layout and the public API from one release to another. Treat that format as unsuitable for long-term storage across Turso versions. Normally, one Turso database file is opened by one OS process. Threads and connections inside that process may share the database safely, while a second process opening the same file is refused with a locking error. Multi-process access relaxes that rule. With the `multiprocess_wal` feature turned on, multiple processes may concurrently open the same `.db` file and use a shared memory file to coordinate WAL reads, writes, and checkpoints.

### When to Use It

 Turn on multi-process access for cases that require:
- Separate OS processes (workers, sidecars, CLI + embedded app) that must read and write the same database file without adding a server tier.
- Deployments with no downtime where the old process and replacement process temporarily share the same database.
- Process-per-connection layouts that want more isolation than threads provide.
 For applications that stay inside one process - including multi-threaded servers backed by a connection pool - the default mode is faster and needs no setup.

### How It Works

 After multi-process access is turned on, Turso places an extra sibling file alongside the database:
| File | Purpose |
| --- | --- |
| `mydb.db` | The database file (unchanged). |
| `mydb.db-wal` | The write-ahead log (unchanged). |
| `mydb.db-tshm` | Turso shared memory. Memory-mapped coordination file that records WAL state, active writer ownership, active checkpointer ownership, reader slots, and the shared page-to-frame index. |

 Each participating process uses mmap on the `.tshm` file and coordinates through:
- A **single-writer** slot. Across the cluster, only one process can hold the writer lock at a time.
- A **single-checkpointer** slot. Processes perform checkpointing one at a time.
- A limited pool of **reader slots**. Active read transactions pin WAL frames so concurrent writers cannot overwrite them and the checkpointer cannot reclaim them.
- A shared **frame index** that lets readers in any process map a page number to the newest WAL frame without starting a WAL scan at the beginning.
 Ownership transfer uses byte-range locks that work across processes on the `.tshm` file: OFD locks on Linux and `fcntl` on macOS. The mmap regions remain the fast path for reading shared metadata.

### Requirements

 Multi-process WAL can be used only when every requirement below is met:
-  **Platform**: A 64-bit Unix-like OS is required, including Linux, macOS, Android, and other supported Unix systems. On Windows, WASM, and 32-bit targets, Turso accepts the flag but ignores it, opening the database in single-process mode.
-  **IO backend**: The selected IO backend must provide shared WAL coordination. The default file-backed backends provide it; memory-only backends and some custom VFSes do not.
-  **Filesystem**: Store the database on a local filesystem with correct POSIX byte-range lock and mmap behavior. The filesystems below are **explicitly rejected** because shared mmap coordination is unsafe on them:
| Filesystem | Reason |
| --- | --- |
| NFS | Advisory lock semantics diverge across clients |
| CIFS / SMB2 | Same condition as above; mmap coherence cannot be guaranteed |
| CephFS | Distributed cache semantics |
| GFS2 | Cluster filesystem; not validated |
| Lustre | Distributed; not validated |
| OCFS2 | Cluster filesystem; not validated |
| AFS, CODA, NCP, 9P (v9fs) | Network/legacy filesystems whose mmap+lock semantics are unreliable |

 If `multiprocess_wal` is enabled and the database is opened on one of these filesystems, Turso returns `InvalidArgument`.
-  **Not in-memory**: `:memory:` and `file::memory:` paths fail because shared coordination needs a durable file.

### Enabling Multi-Process Access

### CLI

 Pass `--experimental-multiprocess-wal`:

```
tursodb --experimental-multiprocess-wal mydb.db

```

 Each `tursodb` (or SDK) process that opens `mydb.db` concurrently has to use the flag. Turso rejects mixed modes; see Mixing Modes.

### Rust SDK

```
use turso::Builder;

let db = Builder::new_local("mydb.db")
    .experimental_multiprocess_wal(true)
    .build()?;

```

### JavaScript / Node.js SDK

```
import { Database } from "@tursodatabase/libsql";

const db = new Database("file:mydb.db", {
    experimental: ["multiprocess_wal"],
});

```

### Python SDK

```
import turso

conn = turso.connect(
    "mydb.db",
    experimental_features="multiprocess_wal",
)

```

### Go SDK

```
db, err := turso.NewDatabase(turso.TursoDatabaseConfig{
    Path:                 "mydb.db",
    ExperimentalFeatures: "multiprocess_wal",
})

```

 Or via DSN:

```
db, err := sql.Open("turso", "mydb.db?experimental=multiprocess_wal")

```

### Concurrency Model

 Multi-process access preserves Turso’s WAL concurrency guarantees across processes:
- **Writers are serialized across processes.** At any moment, only one process (and one connection in that process) owns the writer slot. Writers elsewhere wait for release.
- **Readers never block writers.** Each active read transaction claims a reader slot and pins that snapshot's max-frame. A reader in one process is not invalidated by another process's writer.
- **Checkpointing is serialized across processes.** One checkpointer truncates the WAL at a time; readers with older pinned frames prevent early reclamation no matter which process owns those readers.
- **Snapshots are stable.** Every read transaction sees a consistent snapshot even while another process commits new frames.
 When one process performs schema changes (DDL), other processes pick them up on the next statement through the existing schema refresh path. Prepared statements in sibling processes can return `SchemaUpdated` and re-prepare, matching behavior across connections in one process.

### Mixing Modes

 A database cannot be opened at the same time in single-process mode by one process and multi-process mode by another. During open, Turso checks for an incompatible live opener and fails immediately:
- Opening without `multiprocess_wal` fails when another process has live `.tshm` authority:

>  Database is already open with experimental multiprocess WAL in another process

- Opening with `multiprocess_wal` fails when another process holds the legacy exclusive DB-file lock:

>  Database is already open without experimental multiprocess WAL in another process

 To change modes, first close every existing connection to the database, then reopen with the chosen configuration. The `.tshm` file may remain; Turso uses it again on the next multi-process open and reconstructs state from the WAL when needed. Read-only opens are advisory: if a read-only open requests `multiprocess_wal` and no `.tshm` file exists, Turso uses the legacy read-only WAL path instead of failing. That behavior lets read-only tools inspect a database that a single-process writer closed cleanly.

### Limitations

- The `.tshm` file format has a version and can change in later releases. When the format changes, existing `.tshm` files become invalid; Turso recreates them during open and does not perform cross-version migration.
- Attached databases (via [ATTACH DATABASE](https://docs.turso.tech/sql-reference/statements/attach-database)) use the main connection's multi-process setting.
- This feature is not currently available with MVCC (`BEGIN CONCURRENT`). Choose MVCC inside one process or multi-process WAL with the default isolation model; do not combine them.
- Multi-process mode concurrent-simulator and stress tests are limited to 64-bit Unix targets. Builds for Windows, WASM, and 32-bit platforms accept the flag but leave behavior unchanged.

### See Also

- [Experimental Features](https://docs.turso.tech/sql-reference/experimental-features) - enabling experimental features on every supported surface
- [Transactions](https://docs.turso.tech/sql-reference/statements/transactions) - WAL concurrency behavior and `BEGIN CONCURRENT`
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) — SQLite compatibility matrix

## PRAGMA Statements

### PRAGMA Statements

 PRAGMA statements are SQLite/Turso-specific commands for reading or changing database configuration, retrieving metadata, and controlling database behavior; they are not standard SQL.

### Syntax

```
PRAGMA pragma-name;
PRAGMA pragma-name = value;
PRAGMA pragma-name(value);

```

### Database Metadata

### database_list

 Outputs a row for every attached database.

```
PRAGMA database_list;
-- seq | name | file
-- 0   | main | /path/to/database.db

```

### page_count

 Outputs the total page count for the database file.

```
PRAGMA page_count;
-- 42

```

### page_size

 Reads or changes the database page size; the value can be changed only before table creation begins.

```
PRAGMA page_size;
-- 4096

PRAGMA page_size = 8192;

```

### max_page_count

 Reads or changes the maximum page count permitted for the database file.

```
PRAGMA max_page_count;
PRAGMA max_page_count = 1000000;

```

### freelist_count

 Outputs how many unused pages remain in the database file.

```
PRAGMA freelist_count;

```

### encoding

 Outputs the database text encoding.

```
PRAGMA encoding;
-- UTF-8

```

### schema_version

 Outputs the schema version number, which increases whenever the schema changes.

```
PRAGMA schema_version;
-- 5

```

### application_id

 Reads or changes the application ID stored in the database header. Applications may use this 32-bit integer to identify the database file format.

```
PRAGMA application_id;
PRAGMA application_id = 12345;

```

### user_version

 Reads or changes the user version number, a 32-bit integer reserved for application use.

```
PRAGMA user_version;
PRAGMA user_version = 3;

```

### Schema Introspection

### table_info

 Outputs a row for every column in the named table.

```
PRAGMA table_info(table-name);

```

| Column | Type | Description |
| --- | --- | --- |
| cid | INTEGER | Column index (0-based) |
| name | TEXT | Column name |
| type | TEXT | Declared type name |
| notnull | INTEGER | 1 if NOT NULL constraint exists |
| dflt_value | TEXT | Default value expression, or NULL |
| pk | INTEGER | 1 when the column belongs to the PRIMARY KEY |

```
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT NOT NULL, email TEXT);
PRAGMA table_info(users);
-- cid | name  | type    | notnull | dflt_value | pk
-- 0   | id    | INTEGER | 0       |            | 1
-- 1   | name  | TEXT    | 1       |            | 0
-- 2   | email | TEXT    | 0       |            | 0

```

### table_xinfo

 Like `table_info`, but it also reports hidden columns and extra metadata.

```
PRAGMA table_xinfo(table-name);

```

 Outputs the `table_info` columns plus `hidden`, where 0 indicates a normal column and non-zero identifies hidden columns in virtual tables.

### table_list

 Outputs a row for every table and view in the database.

```
PRAGMA table_list;
-- schema | name    | type  | ncol | wr | strict
-- main   | users   | table | 3    | 0  | 0
-- main   | orders  | table | 4    | 0  | 0

```

### index_list

 Outputs a row for every index defined on the named table.

```
PRAGMA index_list(table-name);
-- seq | name           | unique | origin | partial
-- 0   | idx_email      | 1      | c      | 0

```

| Column | Type | Description |
| --- | --- | --- |
| seq | INTEGER | Index sequence number |
| name | TEXT | Index name |
| unique | INTEGER | 1 if the index is UNIQUE |
| origin | TEXT | Reason code: `c` for CREATE INDEX, `u` for UNIQUE constraint, `pk` for PRIMARY KEY |
| partial | INTEGER | 1 when the index is partial |

### index_info

 Outputs a row for every column in the named index.

```
PRAGMA index_info(index-name);
-- seqno | cid | name
-- 0     | 2   | email

```

### index_xinfo

 Like `index_info`, with extra columns included.

```
PRAGMA index_xinfo(index-name);

```

### function_list

 Outputs a row for every available SQL function.

```
PRAGMA function_list;
-- name | builtin | type | enc | narg | flags

```

### pragma_list

 Outputs every supported PRAGMA command.

```
PRAGMA pragma_list;

```

### Database Configuration

### journal_mode

 Reads or changes the journal mode.

```
PRAGMA journal_mode;
-- wal

PRAGMA journal_mode = wal;

```

 Turso uses WAL (Write-Ahead Logging) mode for journal operation. The rollback journal modes DELETE, TRUNCATE, PERSIST, and MEMORY are not supported. **Turso Extension**: Turso also provides an experimental MVCC journal mode for concurrent writes:

```
PRAGMA journal_mode = mvcc;

```

With MVCC mode active, `BEGIN CONCURRENT` enables optimistic concurrent write transactions. See [Transactions](https://docs.turso.tech/sql-reference/statements/transactions#begin-concurrent) for details.

### cache_size

 Reads or changes the suggested maximum number of database pages kept in memory.

```
PRAGMA cache_size;
PRAGMA cache_size = 2000;          -- positive: number of pages
PRAGMA cache_size = -2000;         -- negative: kilobytes of memory

```

### cache_spill

 Turns cache spilling on or off, which writes dirty pages to the WAL before the cache reaches capacity.

```
PRAGMA cache_spill;
PRAGMA cache_spill = 0;    -- disable
PRAGMA cache_spill = 1;    -- enable

```

### synchronous

 Determines fsync behavior for durability guarantees.

```
PRAGMA synchronous;
PRAGMA synchronous = OFF;    -- no fsync (fastest, risk of corruption on crash)
PRAGMA synchronous = FULL;   -- fsync after every transaction (safest)

```

 Turso supports only `OFF` and `FULL`.

### temp_store

 Selects where temporary tables and indexes are stored.

```
PRAGMA temp_store;
PRAGMA temp_store = 0;    -- DEFAULT
PRAGMA temp_store = 1;    -- FILE
PRAGMA temp_store = 2;    -- MEMORY

```

### busy_timeout

 Configures the busy timeout in milliseconds. If a table is locked, Turso waits up to that many milliseconds before returning SQLITE_BUSY.

```
PRAGMA busy_timeout;
PRAGMA busy_timeout = 5000;   -- 5 seconds

```

### query_only

 When this setting is enabled, database changes are blocked.

```
PRAGMA query_only = 1;    -- enable read-only mode
PRAGMA query_only = 0;    -- disable read-only mode

```

### foreign_keys

 Turns foreign key constraint enforcement on or off.

```
PRAGMA foreign_keys;
PRAGMA foreign_keys = ON;
PRAGMA foreign_keys = OFF;

```

 For SQLite compatibility, foreign key enforcement starts disabled.

### legacy_file_format

 Outputs the legacy file format flag.

```
PRAGMA legacy_file_format;

```

### ignore_check_constraints

 When this setting is enabled, CHECK constraints are skipped.

```
PRAGMA ignore_check_constraints = 1;   -- disable CHECK constraints
PRAGMA ignore_check_constraints = 0;   -- enable CHECK constraints

```

### Integrity Checks

### integrity_check

 Runs a comprehensive integrity check over the full database.

```
PRAGMA integrity_check;
-- ok

PRAGMA integrity_check(N);    -- check only the first N errors

```

 Outputs `ok` when no problems are detected; otherwise, it emits one row for each error.

### quick_check

 Runs a faster integrity check than `integrity_check`, with less coverage.

```
PRAGMA quick_check;

```

### WAL Operations

### wal_checkpoint

 Forces a WAL checkpoint.

```
PRAGMA wal_checkpoint;

```

 A checkpoint copies pages from the WAL file back into the database file.

### Change Data Capture

 **Turso Extension**: Change Data Capture (CDC) is specific to Turso and records all data changes for replication, auditing, and reactive applications.

### capture_data_changes_conn

 Turns on CDC for the current connection and writes captured changes into a selected table.

```
PRAGMA capture_data_changes_conn('mode');
PRAGMA capture_data_changes_conn('mode,table_name');

```

 For backwards compatibility, Turso still accepts the legacy name `unstable_capture_data_changes_conn`.
| Parameter | Description |
| --- | --- |
| mode | Capture mode: `off`, `id`, `before`, `after`, `full` |
| table_name | Custom table used to store changes (default: `turso_cdc`) |

#### Capture Modes

| Mode | Description |
| --- | --- |
| `off` | Disable CDC for this connection |
| `id` | Capture only the primary key/rowid for rows that changed |
| `before` | Capture the row state before updates/deletes |
| `after` | Capture the row state after inserts/updates |
| `full` | Capture before and after row states along with update details |

#### CDC Table Structure

 The CDC table has these columns:
| Column | Type | Description |
| --- | --- | --- |
| change_id | INTEGER | Auto-incrementing unique identifier |
| change_time | INTEGER | Unix epoch timestamp |
| change_type | INTEGER | 1 (INSERT), 0 (UPDATE), -1 (DELETE) |
| table_name | TEXT | Name of the changed table |
| id | varies | Primary key/rowid for the row that changed |
| before | BLOB | Row data from before the change (modes: before, full) |
| after | BLOB | Row data from after the change (modes: after, full) |
| updates | BLOB | Updated column details (mode: full) |

#### CDC Examples

```
-- Enable full CDC
PRAGMA capture_data_changes_conn('full');

-- Make changes
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO users VALUES (1, 'Alice');
UPDATE users SET name = 'Alicia' WHERE id = 1;
DELETE FROM users WHERE id = 1;

-- Query the changes
SELECT change_type, table_name, id FROM turso_cdc;
-- 1  | users | 1    (INSERT)
-- 0  | users | 1    (UPDATE)
-- -1 | users | 1    (DELETE)

-- Use a custom table name
PRAGMA capture_data_changes_conn('full,audit_log');

-- Disable CDC
PRAGMA capture_data_changes_conn('off');

```

 CDC follows transaction boundaries. Turso records changes only when the transaction commits; a rollback creates no CDC entries.

### Encryption

 **Turso Extension**: At-rest encryption is specific to Turso. This experimental feature must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features).

### cipher

 Configures the database encryption cipher.

```
PRAGMA cipher = 'aegis256';

```

 Supported ciphers:
| Cipher | Key Size | Description |
| --- | --- | --- |
| `aes128gcm` | 16 bytes | AES-128 in Galois/Counter Mode |
| `aes256gcm` | 32 bytes | AES-256 in Galois/Counter Mode |
| `aegis128l` | 16 bytes | AEGIS-128L |
| `aegis256` | 32 bytes | AEGIS-256 (recommended) |
| `aegis128x2` | 16 bytes | AEGIS-128 with 2x parallelization |
| `aegis128x4` | 16 bytes | AEGIS-128 with 4x parallelization |
| `aegis256x2` | 32 bytes | AEGIS-256 with 2x parallelization |
| `aegis256x4` | 32 bytes | AEGIS-256 with 4x parallelization |

### hexkey

 Configures the encryption key from a hexadecimal string.

```
PRAGMA hexkey = '2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d';

```

#### Encryption Example

```
-- Set cipher and key before creating tables
PRAGMA cipher = 'aegis256';
PRAGMA hexkey = '2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d';

CREATE TABLE secrets (id INTEGER PRIMARY KEY, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive data');

```

 You can also provide encryption parameters in the database URI:

```
file:database.db?cipher=aegis256&hexkey=2d7a30...

```

 Opening an existing encrypted database requires supplying both the cipher and key as URI parameters.

### Custom Types

 **Turso Extension**: Custom types are specific to Turso.

### list_types

 Outputs all available types, including built-in and custom types, along with their metadata.

```
PRAGMA list_types;
-- type    | parent | encode | decode | default | operators
-- INTEGER |        |        |        |         |
-- REAL    |        |        |        |         |
-- TEXT    |        |        |        |         |
-- BLOB    |        |        |        |         |
-- ANY     |        |        |        |         |

```

 For creating custom types, see [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type).

### See Also

- [Transactions](https://docs.turso.tech/sql-reference/statements/transactions) covers transaction control
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) covers custom type definitions
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) lists all supported PRAGMAs
