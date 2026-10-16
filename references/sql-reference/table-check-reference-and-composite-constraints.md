# Table check reference and composite constraints

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](table-definition-syntax-and-parameters.md) · [Previous phase](table-primary-key-null-and-default-constraints.md) · [Continue](table-foreign-key-actions-and-strict-mode.md)

### CHECK

 Creates a boolean expression that every table row must evaluate as true or NULL. The expression may refer to any column in that same row.

```
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER CHECK (age >= 18),
    salary REAL CHECK (salary > 0)
);

```

### REFERENCES (Foreign Key)

 Creates a foreign key link from this column to a column in another table. See Foreign Key Constraints for the complete syntax.

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id)
);

```

### COLLATE

 Sets the collation sequence for text comparison and column sorting.

| Collation | Description |
| --- | --- |
| `BINARY` | Compares text by raw byte value (default) |
| `NOCASE` | Compares ASCII characters without case sensitivity |
| `RTRIM` | Like `BINARY`, but ignores trailing spaces |

```
CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    name TEXT COLLATE NOCASE,
    code TEXT COLLATE RTRIM
);

```

### Table Constraints

 Table constraints are written after the column definitions and can apply across one or more columns.

### Composite PRIMARY KEY

 Creates a primary key over several columns. The combined values across those key columns must be unique.

```
CREATE TABLE enrollment (
    student_id INTEGER,
    course_id INTEGER,
    semester TEXT,
    grade REAL,
    PRIMARY KEY (student_id, course_id, semester)
);

```

### Composite UNIQUE

 Requires every value combination in the selected columns to be unique.

```
CREATE TABLE assignments (
    employee_id INTEGER,
    project_id INTEGER,
    role TEXT,
    UNIQUE (employee_id, project_id)
);

```
