# Array containment comparison and concatenation operators

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-overlap-and-aggregation.md) · [Continue](array-assignment-tags-and-nested-arrays.md)

### Containment: @>

 Checks whether the left array includes every element from the right array. Equivalent to `array_contains_all(left, right)`.

```
array1 @> array2

```

```
SELECT ARRAY[1,2,3] @> ARRAY[1,2];
-- 1

SELECT ARRAY[1,2,3] @> ARRAY[1,4];
-- 0

```

### Overlap: &&

 Checks whether two arrays have at least one shared element. Equivalent to `array_overlap(left, right)`.

```
array1 && array2

```

```
SELECT ARRAY[1,2,3] && ARRAY[3,4,5];
-- 1

SELECT ARRAY[1,2] && ARRAY[3,4];
-- 0

```

### Comparison: =, !=, <, >, <=, >=

 Arrays compare element by element. Pairwise comparison proceeds left to right; when all compared elements match, the shorter array sorts before the longer array.

```
SELECT ARRAY[1,2,3] = ARRAY[1,2,3];   -- 1
SELECT ARRAY[1,2] < ARRAY[1,3];       -- 1
SELECT ARRAY[1,2] < ARRAY[1,2,3];     -- 1 (shorter is less)

```

### Concatenation: ||

 If either operand is an array, `||` uses array concatenation or element append/prepend behavior rather than string concatenation.

```
SELECT ARRAY[1,2] || ARRAY[3,4];  -- [1,2,3,4]  (array + array)
SELECT ARRAY[1,2] || 3;           -- [1,2,3]    (append element)
SELECT 0 || ARRAY[1,2];           -- [0,1,2]    (prepend element)

```

### Using operators in WHERE clauses

```
CREATE TABLE docs (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO docs VALUES (1, ARRAY['sql','database','tutorial']);
INSERT INTO docs VALUES (2, ARRAY['rust','systems']);
INSERT INTO docs VALUES (3, ARRAY['sql','rust']);

-- Find docs tagged with both 'sql' and 'rust'
SELECT id FROM docs WHERE tags @> ARRAY['sql','rust'];
-- 3

-- Find docs with any overlap with a set of tags
SELECT id FROM docs WHERE tags && ARRAY['database','systems'] ORDER BY id;
-- 1
-- 2

```

---
