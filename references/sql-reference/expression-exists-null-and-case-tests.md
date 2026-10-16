# Expression exists null and case tests

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Previous phase](expression-glob-regexp-between-and-in.md) · [Continue](expression-searched-case-and-scalar-subqueries.md)

### EXISTS Expression

 An EXISTS expression produces `1` when its subquery yields one or more rows; otherwise it produces `0`.

```
[NOT] EXISTS (select-statement)

```

```
SELECT EXISTS (SELECT 1 FROM users WHERE name = 'Alice');  -- 1 if Alice exists

SELECT name FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e
    WHERE e.department_id = d.id
);

```

### IS NULL / IS NOT NULL

 IS NULL checks whether a value is NULL. Unlike `= NULL`, which evaluates to NULL in all cases, `IS NULL` produces `1` or `0`.

```
expression IS NULL
expression IS NOT NULL

```

```
SELECT NULL IS NULL;       -- 1
SELECT NULL = NULL;        -- NULL (not 1!)
SELECT 42 IS NOT NULL;     -- 1
SELECT NULL IS NOT NULL;   -- 0

```

### IS DISTINCT FROM

 IS DISTINCT FROM compares two values while allowing NULL to participate as a comparable value.

```
expression IS [NOT] DISTINCT FROM expression

```

```
SELECT 1 IS DISTINCT FROM 2;       -- 1 (different values)
SELECT 1 IS DISTINCT FROM 1;       -- 0 (same value)
SELECT NULL IS DISTINCT FROM NULL;  -- 0 (both NULL)
SELECT NULL IS DISTINCT FROM 1;     -- 1 (NULL vs non-NULL)
SELECT 1 IS NOT DISTINCT FROM 1;    -- 1

```

### CASE Expression

 A CASE expression adds conditional branching inside SQL expressions.

### Simple CASE

```
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    [ELSE default_result]
END

```
