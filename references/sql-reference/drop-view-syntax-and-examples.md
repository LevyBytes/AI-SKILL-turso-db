# Drop view syntax and examples

[Context index](virtual-tables-delete-and-drop-reference.md)

## DROP VIEW

 Drops a view definition from the database without changing the underlying tables or their data.

### Syntax

```
DROP VIEW [IF EXISTS] [schema-name.]view-name;

```

### Description

 `DROP VIEW` deletes an existing view. Afterward, queries that use the view name fail. The tables named by the view are unchanged.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the view is missing. If no such view exists, the statement does nothing. |
| `schema-name` | Attached database that contains the view. If omitted, this resolves to the main database. |
| `view-name` | View name to drop. |

### Behavior

- INSTEAD OF triggers defined on the dropped view are removed as well.
- For a materialized view, stored data is removed too.

### Examples

### Drop a View

```
DROP VIEW active_users;

```

### Drop a View Only if It Exists

```
DROP VIEW IF EXISTS old_report;

```

### Drop a View in an Attached Database

```
DROP VIEW aux.cached_stats;

```

### See Also

- [CREATE VIEW](https://docs.turso.tech/sql-reference/statements/create-view) when creating views or materialized views
