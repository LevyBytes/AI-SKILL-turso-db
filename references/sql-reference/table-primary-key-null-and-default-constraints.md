# Table primary key null and default constraints

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](table-definition-syntax-and-parameters.md) · [Continue](table-check-reference-and-composite-constraints.md)

### PRIMARY KEY

 Marks the column as the table's primary key. Only one primary key is allowed per table.

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY
);

```

 A column declared `INTEGER PRIMARY KEY` aliases the internal `rowid`; integer values inserted into it become the row identifier directly. This is the fastest primary key form.

#### AUTOINCREMENT

 The `AUTOINCREMENT` keyword is valid only with `INTEGER PRIMARY KEY`. It keeps a counter in the `sqlite_sequence` system table so rowid values from deleted rows are not reused.

```
CREATE TABLE events (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    description TEXT
);

```

 Without `AUTOINCREMENT`, Turso may reuse rowid values after deletions. With `AUTOINCREMENT`, each rowid is guaranteed to exceed every rowid that has ever existed in the table, even after rows have been deleted. Because `AUTOINCREMENT` updates the `sqlite_sequence` table on each insert, it adds a small amount of overhead. Use it only when identifiers must be strictly monotonically increasing.

### NOT NULL

 Disallows NULL in the column. An INSERT or UPDATE that would store NULL in that column raises a constraint error.

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price REAL NOT NULL
);

```

### UNIQUE

 Requires every column value to be distinct. NULL values are treated as distinct from one another, so a UNIQUE column may contain multiple NULL values.

```
CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    email TEXT UNIQUE
);

```

### DEFAULT

 Provides the column's fallback value when an INSERT statement omits that column.

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    status TEXT DEFAULT 'pending',
    created_at TEXT DEFAULT CURRENT_TIMESTAMP,
    quantity INTEGER DEFAULT 1
);

```

 The default expression can be:

| Expression | Example |
| --- | --- |
| A literal value | `DEFAULT 42`, `DEFAULT 'active'` |
| NULL | `DEFAULT NULL` |
| `CURRENT_TIME` | Current time as `HH:MM:SS` |
| `CURRENT_DATE` | Current date as `YYYY-MM-DD` |
| `CURRENT_TIMESTAMP` | Current datetime as `YYYY-MM-DD HH:MM:SS` |
| A parenthesized expression | `DEFAULT (1 + 1)` |
