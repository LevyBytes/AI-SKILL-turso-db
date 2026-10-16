# Alter table and column renaming

[Context index](alter-analyze-attach-and-domain-reference.md) · [Continue](alter-table-column-addition-restrictions.md)

## ALTER TABLE

 Change an existing table's structure.

### Syntax

```
ALTER TABLE [schema-name.]table-name RENAME TO new-table-name;
ALTER TABLE [schema-name.]table-name RENAME COLUMN old-column-name TO new-column-name;
ALTER TABLE [schema-name.]table-name ADD COLUMN column-def;
ALTER TABLE [schema-name.]table-name DROP COLUMN column-name;

```

### Description

 `ALTER TABLE` changes an existing table's structure in place, so you do not need to rebuild the table or duplicate its data. Turso supports four forms of ALTER TABLE: rename the table, rename a column, add a new column, or drop an existing column.

### Parameters

| Parameter | Description |
| --- | --- |
| `schema-name` | Name the attached database that holds the table. If omitted, this uses the main database. |
| `table-name` | The table name that will be altered. |

### RENAME TO

 Rename an existing table.

```
ALTER TABLE old_name RENAME TO new_name;

```

 When a table is renamed, every table reference is updated in:
- Triggers that reference the table
- Indexes on the table
- Foreign key constraints, whether the table is the parent or child
- CHECK constraints
- Views that reference the table
 The replacement table name cannot match any table, view, or index already present in the same database.

### Example

```
CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
);

ALTER TABLE customers RENAME TO clients;

-- The table is now accessible as 'clients'
SELECT * FROM clients;

```

### RENAME COLUMN

 Change the name of a table column that already exists.

```
ALTER TABLE table-name RENAME COLUMN old-column-name TO new-column-name;

```

 When a column is renamed, all column references are rewritten in:
- Indexes referring to the column, including expression indexes
- Triggers that reference the column
- CHECK constraints that refer to the column
- Foreign key constraint definitions
