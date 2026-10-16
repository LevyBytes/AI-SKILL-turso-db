# Select columns aliases and from

[Context index](select-and-transaction-reference.md) · [Continue](select-filtering-comparison-and-null-tests.md)

## SELECT

 Reads rows from one table or several tables. In Turso, SELECT is the main read operation and can filter, join, aggregate, sort, use subqueries, and perform set operations.

### Syntax

```
[WITH cte_name AS (select_statement) [, ...]]
SELECT [DISTINCT] result_column [, ...]
  [FROM table_or_subquery [join_clause ...]]
  [WHERE expression]
  [GROUP BY expression [, ...]]
  [HAVING expression]
  [WINDOW window_name AS (window_definition) [, ...]]
  [compound_operator select_statement]
  [ORDER BY ordering_term [, ...]]
  [LIMIT expression [OFFSET expression]]

```

### Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `result_column` | expression, `*`, or `table.*` | Return columns or expressions. Use `*` to include every column |
| `table_or_subquery` | identifier or subquery | A table name, an aliased table, or a parenthesized SELECT |
| `expression` | expression | Any valid SQL expression |
| `ordering_term` | expression + direction | Expression plus optional ASC/DESC and NULLS FIRST/LAST |
| `compound_operator` | keyword | UNION, UNION ALL, INTERSECT, or EXCEPT |
| `cte_name` | identifier | Common Table Expression name |
| `window_name` | identifier | Reusable window definition name |

### Basic Queries

### Selecting Columns

```
-- All columns
SELECT * FROM employees;

-- Specific columns
SELECT name, department FROM employees;

-- Expressions and aliases
SELECT name, salary * 12 AS annual_salary FROM employees;

```

### Column and Table Aliases

 Apply `AS` when giving columns or tables aliases. For column aliases, the `AS` keyword can be omitted.

```
SELECT e.name, e.salary * 12 annual_pay
FROM employees AS e;

```

### FROM Clause

 The FROM clause identifies which tables supply the query data. It can take table names, aliased tables, subqueries, and join expressions.

```
-- Single table
SELECT * FROM employees;

-- Subquery as table source
SELECT * FROM (SELECT name, salary FROM employees WHERE salary > 50000) AS high_earners;

```
