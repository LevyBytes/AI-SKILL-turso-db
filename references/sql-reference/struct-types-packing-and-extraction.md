# Struct types packing and extraction

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](custom-type-inspection-and-alter-table.md) · [Continue](union-types-tags-and-extraction.md)

### STRUCT

 A STRUCT combines several named fields into one column. Dot notation lets you read or filter by a specific field:

```
CREATE TYPE point AS STRUCT(x INT, y INT);

CREATE TABLE locations (
    id INTEGER PRIMARY KEY,
    pos point
) STRICT;

INSERT INTO locations VALUES (1, struct_pack(10, 20));
INSERT INTO locations VALUES (2, struct_pack(30, 40));

-- Read fields with dot notation
SELECT pos.x, pos.y FROM locations WHERE id = 1;
-- 10|20

-- Filter and order by fields
SELECT id, pos.x FROM locations WHERE pos.y > 25 ORDER BY pos.x;
-- 2|30

```

 Dot notation is accepted anywhere a column expression is valid, including SELECT, WHERE, ORDER BY, GROUP BY, HAVING, and aggregate functions:

```
CREATE TYPE address AS STRUCT(street TEXT, city TEXT, zip TEXT);
CREATE TABLE people(name TEXT, home address) STRICT;

INSERT INTO people VALUES ('Alice', struct_pack('123 Main St', 'Springfield', '62704'));
INSERT INTO people VALUES ('Bob', struct_pack('456 Oak Ave', 'Springfield', '62701'));

SELECT home.city, COUNT(*) FROM people GROUP BY home.city;
-- Springfield|2

```

 If a table name conflicts with a column name, the table reference takes precedence. Use an alias to access the struct field:

```
-- t.x here is table.column, not column.field
SELECT t.x FROM t;

-- Use an alias to access the struct field
SELECT s.pos.x FROM locations AS s;

```

#### struct_pack()

 Builds a struct value for INSERT and UPDATE. Arguments are positional and follow the field order from the type definition.

```
INSERT INTO locations VALUES (1, struct_pack(10, 20));
UPDATE locations SET pos = struct_pack(99, 88) WHERE id = 1;

```

#### struct_extract()

 Functional equivalent of dot notation: `struct_extract(col, 'field')` matches `col.field`. It is mainly useful for expression indexes, where dot notation is unavailable:

```
CREATE INDEX idx_x ON locations(struct_extract(pos, 'x'));

```
