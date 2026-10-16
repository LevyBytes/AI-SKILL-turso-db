# Select filtering comparison and null tests

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Continue](select-case-and-join-types.md)

### WHERE Clause

 Uses a condition to filter rows. The result includes only rows for which the expression evaluates to true.

```
SELECT * FROM employees WHERE department = 'Engineering';

SELECT * FROM orders WHERE total > 100 AND status != 'cancelled';

```

### Comparison Operators

| Operator | Description |
| --- | --- |
| `=` | Equal |
| `!=` or `<>` | Not equal |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal |
| `>=` | Greater than or equal |
| `IS` | Equal (NULL-safe) |
| `IS NOT` | Not equal (NULL-safe) |
| `IS DISTINCT FROM` | Not identical, with NULL-safe SQL standard behavior |
| `IS NOT DISTINCT FROM` | Identical (NULL-safe, SQL standard) |

### Logical Operators

 Use `AND`, `OR`, and `NOT` to combine conditions.

```
SELECT * FROM products
WHERE (category = 'Electronics' OR category = 'Books')
  AND price < 50
  AND NOT discontinued;

```

### Pattern Matching

```
-- LIKE: case-insensitive pattern matching (% = any chars, _ = one char)
SELECT * FROM employees WHERE name LIKE 'J%';

-- GLOB: case-sensitive pattern matching (* = any chars, ? = one char)
SELECT * FROM files WHERE path GLOB '*.txt';

-- REGEXP: regular expression matching
SELECT * FROM logs WHERE message REGEXP '^ERROR:';

```

### Range and Membership Tests

```
-- BETWEEN (inclusive on both ends)
SELECT * FROM orders WHERE total BETWEEN 100 AND 500;

-- IN with a list
SELECT * FROM employees WHERE department IN ('Engineering', 'Design', 'Product');

-- IN with a subquery
SELECT * FROM employees WHERE department_id IN (SELECT id FROM departments WHERE active = 1);

```

### NULL Tests

```
SELECT * FROM employees WHERE manager_id IS NULL;

SELECT * FROM employees WHERE phone IS NOT NULL;

```
