# Drop index syntax and examples

[Context index](virtual-tables-delete-and-drop-reference.md)

## DROP INDEX

 Drops an index from the database without changing the indexed table or its rows.

### Syntax

```
DROP INDEX [IF EXISTS] [schema-name.]index-name;

```

### Description

 `DROP INDEX` deletes an existing index. Once it is gone, the query planner cannot choose that index for optimization. Table contents stay the same.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the index is missing. If no such index exists, the statement does nothing. |
| `schema-name` | Attached database that contains the index. If omitted, this resolves to the main database. |
| `index-name` | Index name to drop. |

### Behavior

- You can drop only indexes that were created explicitly. Indexes generated for PRIMARY KEY and UNIQUE constraints cannot be removed directly - drop the table or use ALTER TABLE instead.
- Removing an index can slow queries that depended on it, but query results remain correct.

### Examples

### Drop an Index

```
DROP INDEX idx_users_email;

```

### Drop an Index Only if It Exists

```
DROP INDEX IF EXISTS idx_old_search;

```

### Drop an Index in an Attached Database

```
DROP INDEX aux.idx_sessions_token;

```

### See Also

- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) when creating indexes
- [EXPLAIN](https://docs.turso.tech/sql-reference/statements/explain) when checking query plans after an index drop
