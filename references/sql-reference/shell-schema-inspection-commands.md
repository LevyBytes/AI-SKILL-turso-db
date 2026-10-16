# Shell schema inspection commands

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Continue](shell-output-modes-and-null-display.md)

### .tables

 Show every database table, with an optional pattern filter.

```
.tables [PATTERN]

```

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE departments (id INTEGER PRIMARY KEY, name TEXT);

```

```
tursodb> .tables
employees
departments

tursodb> .tables emp%
employees

```

### .schema

 Show the CREATE statement for one table, or for all tables when no argument is supplied.

```
.schema [TABLE]

```

```
tursodb> .schema employees
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);

tursodb> .schema
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE departments (id INTEGER PRIMARY KEY, name TEXT);

```

### .indexes

 Display indexes by name, optionally limited to one table.

```
.indexes [TABLE]

```

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT, department TEXT);
CREATE INDEX idx_dept ON employees(department);
CREATE INDEX idx_name ON employees(name);

```

```
tursodb> .indexes
idx_dept
idx_name

tursodb> .indexes employees
idx_dept
idx_name

```

### .databases

 List all attached databases.

```
tursodb> .databases
main: /path/to/mydata.db r/w

```

### Output Control
