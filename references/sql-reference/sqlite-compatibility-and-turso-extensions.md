# SQLite compatibility and Turso extensions

[Context index](shell-and-compatibility-workflows.md)

## SQLite Compatibility

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
