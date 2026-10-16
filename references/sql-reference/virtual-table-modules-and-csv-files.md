# Virtual table modules and csv files

[Context index](virtual-tables-delete-and-drop-reference.md) · [Continue](virtual-table-number-and-date-series.md)

## CREATE VIRTUAL TABLE

 CREATE VIRTUAL TABLE defines a table whose rows are produced dynamically by a module instead of being stored in the database file. Virtual tables let SQL query external sources, generated data, and table-valued functions.

### Syntax

```
CREATE VIRTUAL TABLE [IF NOT EXISTS] [schema-name.]table-name
    USING module-name [(module-arguments)];

```

### Description

 Row storage and lookup for a virtual table are handled by its module. That module defines the columns, returned data, and query-planning choices. You can query virtual tables with SELECT as you would ordinary tables, though INSERT, UPDATE, and DELETE work only when the module provides write behavior.

### Clauses

| Clause | Description |
| --- | --- |
| `IF NOT EXISTS` | Prevents the error normally raised when a table using that name is already present. |
| `schema-name` | Attached database where the virtual table should be created. If omitted, this uses `main`. |
| `table-name` | Name to assign to the virtual table. |
| `module-name` | Module name that supplies the virtual table implementation. |
| `module-arguments` | Comma-separated values provided to the module. Their syntax and interpretation depend on the selected module. |

### Available Modules

### csv

 The `csv` module reads a CSV file and presents the file contents as a read-only table. Before using it, load the csv extension with `load_extension('csv')`.

| Argument | Description |
| --- | --- |
| `filename` | Path of the CSV file that csv should read. |
| `header` | Use `yes` when row one supplies column names. The default is `no`. |
| `columns` | Expected column count when `header` is `no`. |
| `schema` | Explicit CREATE TABLE schema that declares column names and column types. |

### generate_series

 `generate_series` is a built-in table-valued function that emits integer values in sequence. No extension load step is required.

| Argument | Description |
| --- | --- |
| `start` | Inclusive starting value for the sequence. |
| `stop` | Inclusive ending value for the sequence. |
| `step` | Step size between generated values. Defaults to `1`. |

 `generate_series` may be called from the FROM clause as either a virtual table or a table-valued function.

### Examples

### Read a CSV File

```
-- Load the csv extension
SELECT load_extension('csv');

-- Create a virtual table from a CSV file with a header row
CREATE VIRTUAL TABLE employees USING csv(
    filename='employees.csv',
    header=yes
);

-- Query the CSV data with SQL
SELECT * FROM employees WHERE department = 'Engineering';

```
