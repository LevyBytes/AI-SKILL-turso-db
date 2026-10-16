# Drop table syntax and examples

[Context index](virtual-tables-delete-and-drop-reference.md)

## DROP TABLE

 Drops a table from the database together with its data, indexes, triggers, and constraints.

### Syntax

```
DROP TABLE [IF EXISTS] [schema-name.]table-name;

```

### Description

 `DROP TABLE` deletes the table definition and every row stored in that table. Related indexes, triggers, and constraints are deleted too.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the table is missing. If no such table exists, the statement does nothing. |
| `schema-name` | Attached database that contains the table. If omitted, this resolves to the main database. |
| `table-name` | Table name to drop. |

### Behavior

- Every row in the table is deleted.
- Indexes built on the table are removed.
- Triggers tied to the table are removed.
- The entry for the table is deleted from `sqlite_schema`.
- Foreign key constraints that point at the dropped table become invalid. Turso allows dropping a table even when other tables reference it through foreign keys.
- `DROP TABLE` cannot run while another statement in the same connection is reading from or writing to that table.

### Examples

### Drop a Table

```
DROP TABLE users;

```

### Drop a Table Only if It Exists

```
DROP TABLE IF EXISTS temporary_results;

```

### Drop a Table in an Attached Database

```
DROP TABLE aux.sessions;

```

### See Also

- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when creating tables
- [ALTER TABLE](https://docs.turso.tech/sql-reference/statements/alter-table) when modifying tables
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) when rows should be removed while keeping the table
