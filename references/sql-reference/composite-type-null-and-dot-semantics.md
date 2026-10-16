# Composite type null and dot semantics

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](union-types-tags-and-extraction.md) · [Continue](custom-array-types-and-monetary-values.md)

### NULL handling

 NULL propagates through every struct and union operation:

```
CREATE TYPE number AS UNION(i INT, f REAL);
CREATE TABLE values_t(id INT, val number) STRICT;

INSERT INTO values_t VALUES (1, union_value('i', 42));
INSERT INTO values_t VALUES (2, union_value('f', 3.14));
INSERT INTO values_t VALUES (3, NULL);

SELECT id, val.i, val.f FROM values_t ORDER BY id;
-- 1|42|
-- 2||3.14
-- 3||

```

### Dot notation precedence

 For an ambiguous dot expression like `a.b`, where `a` might be either a table name or a column name, table references take precedence over struct field access. This matches DuckDB's resolution rules.

```
CREATE TYPE point AS STRUCT(x INT, y INT);
CREATE TABLE t(x INT, t point) STRICT;
INSERT INTO t VALUES (99, struct_pack(10, 20));

-- t.x resolves as table.column (99), not column.field (10)
SELECT t.x FROM t;
-- 99

-- Use an alias to reach the struct field
SELECT s.t.x FROM t AS s;
-- 10

```

 For `a.b.c`, resolution tries database.table.column first, then table.column.field.

### Inline types not supported

 Column definitions cannot declare inline STRUCT/UNION types. Define the type with CREATE TYPE before using it:

```
-- This will error:
CREATE TABLE t(data STRUCT(x INT, y INT)) STRICT;

-- Do this instead:
CREATE TYPE point AS STRUCT(x INT, y INT);
CREATE TABLE t(data point) STRICT;

```
