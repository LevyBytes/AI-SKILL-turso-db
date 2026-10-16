# Expression searched case and scalar subqueries

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Previous phase](expression-exists-null-and-case-tests.md) · [Continue](expression-raise-and-operator-precedence.md)

### Searched CASE

```
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    [ELSE default_result]
END

```

 When no WHEN branch is selected and no ELSE branch exists, CASE evaluates to NULL.

```
-- Simple CASE
SELECT name,
    CASE status
        WHEN 'A' THEN 'Active'
        WHEN 'I' THEN 'Inactive'
        ELSE 'Unknown'
    END AS status_text
FROM users;

-- Searched CASE
SELECT name, score,
    CASE
        WHEN score >= 90 THEN 'A'
        WHEN score >= 80 THEN 'B'
        WHEN score >= 70 THEN 'C'
        ELSE 'F'
    END AS grade
FROM students;

```

### Scalar Subqueries

 A parenthesized subquery that yields one value may appear where an expression is allowed.

```
(select-statement)

```

 The subquery must produce one column. Its first row supplies the scalar value; zero rows yield NULL. Use an explicit ordering and limit when choosing a particular row.

```
SELECT name,
    (SELECT COUNT(*) FROM orders WHERE orders.user_id = users.id) AS order_count
FROM users;

SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);

```
