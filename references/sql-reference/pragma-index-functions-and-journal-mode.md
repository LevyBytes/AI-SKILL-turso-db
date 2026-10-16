# Pragma index functions and journal mode

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Previous phase](pragma-schema-versions-and-table-inspection.md) · [Continue](pragma-cache-synchronization-and-query-settings.md)

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
