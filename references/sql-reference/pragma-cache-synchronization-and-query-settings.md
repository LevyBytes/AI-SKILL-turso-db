# Pragma cache synchronization and query settings

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Previous phase](pragma-index-functions-and-journal-mode.md) · [Continue](pragma-integrity-checks-and-wal-checkpoints.md)

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
