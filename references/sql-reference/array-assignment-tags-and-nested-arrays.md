# Array assignment tags and nested arrays

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-containment-comparison-and-concatenation-operators.md) · [Continue](array-type-checking.md)

### Subscript Assignment

 UPDATE statements can modify individual array elements through subscript syntax.

```
UPDATE table SET column[index] = value WHERE ...;

```

```
CREATE TABLE t (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO t VALUES (1, ARRAY['a','b','c']);

UPDATE t SET tags[0] = 'X' WHERE id = 1;
SELECT tags FROM t;
-- ["X","b","c"]

-- Multiple subscripts in one UPDATE
UPDATE t SET tags[0] = 'A', tags[2] = 'C' WHERE id = 1;
SELECT tags FROM t;
-- ["A","b","C"]

```

 Assignments beyond the array bounds are ignored without an error, leaving the array unchanged.

---

### Examples

### Tagging system

```
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    tags TEXT[]
) STRICT;

INSERT INTO articles VALUES (1, 'Getting Started with SQL', ARRAY['sql','beginner','tutorial']);
INSERT INTO articles VALUES (2, 'Advanced Indexing', ARRAY['sql','performance','advanced']);
INSERT INTO articles VALUES (3, 'Rust for Systems', ARRAY['rust','systems']);

-- Find articles tagged 'sql'
SELECT title FROM articles WHERE array_contains(tags, 'sql');

-- Find articles with both 'sql' and 'advanced'
SELECT title FROM articles WHERE tags @> ARRAY['sql','advanced'];
-- Advanced Indexing

-- Count tags per article
SELECT title, array_length(tags) AS tag_count FROM articles;

-- Collect distinct comma-joined tag lists (one string per article)
SELECT array_agg(DISTINCT tag) FROM (
    SELECT array_to_string(tags, ',') AS tag FROM articles
);

```

### Multi-dimensional arrays

```
CREATE TABLE matrices (
    id INTEGER PRIMARY KEY,
    data INTEGER[][]
) STRICT;

INSERT INTO matrices VALUES (1, ARRAY[ARRAY[1,2,3], ARRAY[4,5,6]]);

-- Access a row
SELECT data[0] FROM matrices WHERE id = 1;
-- [1,2,3]

-- Access a single element
SELECT data[1][2] FROM matrices WHERE id = 1;
-- 6

-- Get number of rows
SELECT array_length(data) FROM matrices WHERE id = 1;
-- 2

```
