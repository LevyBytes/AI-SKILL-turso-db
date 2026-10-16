# Insert syntax and row values

[Context index](explain-insert-and-replace-reference.md) · [Continue](insert-select-and-default-values.md)

## INSERT

 Creates one or more table rows from literal VALUES, data returned by a SELECT, or column DEFAULT definitions.

### Syntax

```
INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  VALUES (expression [, ...]) [, (expression [, ...]) ...]
  [RETURNING result_column [, ...]];

INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  select_statement
  [RETURNING result_column [, ...]];

INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  DEFAULT VALUES
  [RETURNING result_column [, ...]];

```

### Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `conflict_action` | keyword | One of REPLACE, IGNORE, ABORT, ROLLBACK, or FAIL; determines how constraint failures are handled. |
| `table_name` | identifier | The table that will receive the inserted rows. |
| `column_name` | identifier | Target column that receives a value; columns left out use their default value or NULL. |
| `expression` | expression | A value to insert, or the keyword `DEFAULT` when the column's default should be used. It must correspond to the target column by position or name. |
| `select_statement` | SELECT query | A query whose returned rows are added to the table. |
| `result_column` | expression | Column or expression returned for every inserted row. |

### Description

 INSERT creates rows in the named table and supports three variants:
- **VALUES**: Adds one or more rows using explicit values.
- **SELECT**: Adds rows produced by a query result set.
- **DEFAULT VALUES**: Creates one row where each column takes its default, or NULL when no default exists.
 If a column list appears, explicit data is assigned only to those columns; remaining columns fall back to defaults or NULL. Without a column list, the statement must include values for all columns, following the table's declared column order.

### Inserting Rows

### Single Row

```
INSERT INTO employees (name, department, salary)
VALUES ('Alice', 'Engineering', 95000);

```

### Multiple Rows

 Use several comma-delimited parenthesized groups.

```
INSERT INTO employees (name, department, salary) VALUES
  ('Bob', 'Design', 82000),
  ('Carol', 'Engineering', 97000),
  ('Dave', 'Marketing', 74000);

```

### All Columns

 If no column list is present, include one value for each column, ordered as defined in the table.

```
-- Assuming employees has columns: id, name, department, salary
INSERT INTO employees VALUES (1, 'Alice', 'Engineering', 95000);

```
