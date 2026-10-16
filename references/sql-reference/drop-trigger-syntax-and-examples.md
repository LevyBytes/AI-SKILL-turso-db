# Drop trigger syntax and examples

[Context index](virtual-tables-delete-and-drop-reference.md)

## DROP TRIGGER

 DROP TRIGGER deletes a trigger from the database schema. Afterward, that trigger no longer fires for the table events it was associated with.

### Syntax

```
DROP TRIGGER [IF EXISTS] [schema-name.]trigger-name;

```

### Description

 DROP TRIGGER deletes the specified trigger from the database. It must exist unless `IF EXISTS` is present.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the trigger is missing. If no matching trigger is found, the statement does nothing. |
| `schema-name` | Attached database that contains the trigger. If omitted, Turso looks for the trigger in the main database. |
| `trigger-name` | Trigger name to remove. |

 Dropping a trigger leaves the associated table's data unchanged. Only the trigger definition is deleted from the schema.

### Examples

### Drop a Trigger

```
CREATE TRIGGER log_insert AFTER INSERT ON users
BEGIN
    INSERT INTO audit_log (action, user_id) VALUES ('insert', NEW.id);
END;

-- Remove the trigger
DROP TRIGGER log_insert;

```

### Drop a Trigger with IF EXISTS

```
-- Safe to run even if the trigger does not exist
DROP TRIGGER IF EXISTS log_insert;

```

### Drop a Trigger in an Attached Database

```
DROP TRIGGER IF EXISTS aux.log_insert;

```

### See Also

- [CREATE TRIGGER](https://docs.turso.tech/sql-reference/statements/create-trigger) when creating triggers
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when working with table definitions
