# Expression glob regexp between and in

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Previous phase](expression-casts-collations-and-like.md) · [Continue](expression-exists-null-and-case-tests.md)

### GLOB Operator

 The GLOB operator uses Unix-style wildcards for case-sensitive pattern matching. `*` stands for any character sequence, while `?` stands for one character.

```
expression [NOT] GLOB pattern

```

```
SELECT 'Hello' GLOB 'H*';      -- 1
SELECT 'Hello' GLOB 'h*';      -- 0 (case-sensitive)
SELECT 'Hello' GLOB 'H?llo';   -- 1
SELECT 'Hello' GLOB 'H[a-z]*'; -- 1 (character class)

```

### REGEXP Operator

 The REGEXP operator matches text with regular expressions. It needs the regexp extension, which is loaded by default.

```
expression [NOT] REGEXP pattern

```

```
SELECT 'Hello123' REGEXP '[A-Za-z]+[0-9]+';  -- 1
SELECT 'test@email.com' REGEXP '^[^@]+@[^@]+\.[^@]+$';  -- 1

```

### BETWEEN Expression

 A BETWEEN expression checks whether a value is inside an inclusive range.

```
expression [NOT] BETWEEN low AND high

```

 BETWEEN has the same meaning as `expression >= low AND expression <= high`:

```
SELECT 5 BETWEEN 1 AND 10;     -- 1
SELECT 5 NOT BETWEEN 1 AND 3;  -- 1
SELECT 'b' BETWEEN 'a' AND 'c'; -- 1

```

### IN Expression

 An IN expression checks a value against every value supplied by a list or returned from a subquery.

```
expression [NOT] IN (value1, value2, ...)
expression [NOT] IN (select-statement)

```

```
SELECT 3 IN (1, 2, 3, 4, 5);           -- 1
SELECT 'red' NOT IN ('blue', 'green');   -- 1

SELECT name FROM users
WHERE id IN (SELECT user_id FROM orders WHERE total > 100);

```
