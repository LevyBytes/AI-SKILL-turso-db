# Table definition syntax and parameters

[Context index](indexes-tables-and-materialized-views.md) · [Continue](table-primary-key-null-and-default-constraints.md)

## CREATE TABLE

 Define a new database table with columns, data types, and constraints.

### Syntax

```
CREATE TABLE [IF NOT EXISTS] [schema-name.]table-name (
    column-def [, column-def ...] [, table-constraint [, table-constraint ...]]
) [STRICT];

```

### Column Definition

```
column-name [type-name] [column-constraint ...]

```

### Column Constraints

```
PRIMARY KEY [ASC | DESC] [AUTOINCREMENT]
NOT NULL
UNIQUE
DEFAULT expression
CHECK (expression)
REFERENCES foreign-table [(column-name)] [foreign-key-actions]
COLLATE collation-name

```

### Table Constraints

```
PRIMARY KEY (column-name [, column-name ...])
UNIQUE (column-name [, column-name ...])
CHECK (expression)
FOREIGN KEY (column-name [, column-name ...]) REFERENCES foreign-table [(column-name [, column-name ...])] [foreign-key-actions]

```

### Description

 `CREATE TABLE` creates a table in the database. Its name must not duplicate another name in the database schema. A table contains one or more column definitions, plus optional constraints at the table level.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF NOT EXISTS` | Avoids an error when a table using that name already exists. If the table is already present, the statement does nothing. |
| `schema-name` | Names an attached database. If omitted, the main database is used by default. |
| `table-name` | The table name to create. |
| `STRICT` | Turns on strict type checking for the table. See STRICT Tables. |

### Column Definitions

 A column definition gives the column name, may include a type, and may include any number of column constraints. If no type is given, the column can store any storage class. When a type name is present, it sets the column's [type affinity](https://docs.turso.tech/sql-reference/data-types#type-affinity), affecting how inserted values are stored.

```
CREATE TABLE measurements (
    id INTEGER PRIMARY KEY,
    sensor_name TEXT NOT NULL,
    reading REAL,
    raw_data BLOB,
    notes                    -- no type: accepts any storage class
);

```

### Column Constraints
