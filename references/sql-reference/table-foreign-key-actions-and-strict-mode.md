# Table foreign key actions and strict mode

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](table-definition-syntax-and-parameters.md) · [Previous phase](table-check-reference-and-composite-constraints.md) · [Continue](table-type-restrictions-and-constraint-examples.md)

### Table-Level CHECK

 A table-level CHECK constraint may refer to more than one column.

```
CREATE TABLE reservations (
    id INTEGER PRIMARY KEY,
    start_date TEXT NOT NULL,
    end_date TEXT NOT NULL,
    CHECK (end_date > start_date)
);

```

### Foreign Key Constraints

 Foreign key constraints maintain referential integrity across tables by requiring child-table values to match existing parent-table values. Enforcement of foreign keys is disabled by default. Turn it on with `PRAGMA foreign_keys = ON;` before running operations that need checking.

#### Syntax

```
-- Column constraint form (single column)
column-name type REFERENCES parent-table(parent-column)
    [ON DELETE action] [ON UPDATE action]
    [DEFERRABLE INITIALLY DEFERRED]

-- Table constraint form (single or multiple columns)
FOREIGN KEY (column-name [, column-name ...])
    REFERENCES parent-table(parent-column [, parent-column ...])
    [ON DELETE action] [ON UPDATE action]
    [DEFERRABLE INITIALLY DEFERRED]

```

#### Foreign Key Actions

 The `ON DELETE` and `ON UPDATE` clauses define the effect on child rows when the referenced parent row is deleted or updated.

| Action | Description |
| --- | --- |
| `NO ACTION` | Raises an error when the foreign key constraint is violated (default) |
| `RESTRICT` | Like `NO ACTION`, except the check occurs immediately instead of at statement end |
| `CASCADE` | Deletes or updates each child row that references the parent row |
| `SET NULL` | Sets child-row foreign key columns to NULL |
| `SET DEFAULT` | Sets child-row foreign key columns to their default values |

```
CREATE TABLE departments (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    dept_id INTEGER,
    manager_id INTEGER,
    FOREIGN KEY (dept_id) REFERENCES departments(id) ON DELETE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES employees(id) ON DELETE SET NULL
);

```

### STRICT Tables

 STRICT tables check types at the storage layer. For each STRICT column, an inserted value has to match its declared type or convert to that type without loss.

```
CREATE TABLE sensor_data (
    id INTEGER PRIMARY KEY,
    reading REAL NOT NULL,
    label TEXT,
    payload BLOB
) STRICT;

```
