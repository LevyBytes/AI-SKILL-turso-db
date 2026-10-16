# Alter table column removal and schema evolution

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](alter-table-and-column-renaming.md) · [Previous phase](alter-table-addition-and-drop-restrictions.md)

### Examples

#### Drop a column

```
ALTER TABLE users DROP COLUMN phone;

```

#### Verify columns after dropping

```
CREATE TABLE example (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    old_field TEXT,
    active INTEGER DEFAULT 1
);

ALTER TABLE example DROP COLUMN old_field;

PRAGMA table_info(example);
-- cid | name   | type    | notnull | dflt_value | pk
-- 0   | id     | INTEGER | 0       | NULL       | 1
-- 1   | name   | TEXT    | 1       | NULL       | 0
-- 2   | active | INTEGER | 0       | 1          | 0

```

### Examples

### Complete Schema Evolution

```
-- Create initial table
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    body TEXT
);

-- Add an author column with a foreign key
ALTER TABLE articles ADD COLUMN author_id INTEGER REFERENCES users(id);

-- Add a publication status
ALTER TABLE articles ADD COLUMN status TEXT NOT NULL DEFAULT 'draft';

-- Rename a column for clarity
ALTER TABLE articles RENAME COLUMN body TO content;

-- Add a nullable timestamp column
ALTER TABLE articles ADD COLUMN updated_at TEXT;

-- Rename the table
ALTER TABLE articles RENAME TO posts;

-- Final schema
PRAGMA table_info(posts);
-- cid | name       | type    | notnull | dflt_value        | pk
-- 0   | id         | INTEGER | 0       | NULL              | 1
-- 1   | title      | TEXT    | 1       | NULL              | 0
-- 2   | content    | TEXT    | 0       | NULL              | 0
-- 3   | author_id  | INTEGER | 0       | NULL              | 0
-- 4   | status     | TEXT    | 1       | 'draft'           | 0
-- 5   | updated_at | TEXT    | 0       | NULL              | 0

```

### See Also

- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when you need to create tables
- [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table) when you need to remove tables
- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) when you need to index columns
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for details about column types and STRICT table types
