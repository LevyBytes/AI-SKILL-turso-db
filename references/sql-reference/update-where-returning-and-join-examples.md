# Update where returning and join examples

[Context index](update-upsert-and-vacuum-reference.md) · [Setup and preceding context](update-set-from-and-conflicts.md) · [Continue](update-returning-example.md)

### WHERE Clause

 `WHERE` selects the rows to modify. Rows are changed only when the expression is true; without `WHERE`, the update touches every row in the table.

### RETURNING Clause

 `RETURNING` makes an `UPDATE` return the post-update values for each row it changes. Returned columns may be expressions that refer to the updated values.

```
UPDATE accounts
SET balance = balance - 100
WHERE account_id = 42
RETURNING account_id, balance;
-- Returns: 42|900

```

### ORDER BY and LIMIT

 `ORDER BY` and `LIMIT` narrow the set of rows changed. `ORDER BY` controls the consideration order, while `LIMIT` sets the maximum number of rows modified. `ORDER BY` is valid only when `LIMIT` is also present.

```
UPDATE logs
SET archived = 1
ORDER BY created_at ASC
LIMIT 1000;

```

 `LIMIT` can include an optional `OFFSET` that skips rows before the limit is applied:

```
UPDATE logs
SET archived = 1
ORDER BY created_at ASC
LIMIT 1000 OFFSET 500;

```

### Examples

### Update rows matching a condition

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT, salary REAL, department TEXT);
INSERT INTO employees VALUES (1, 'Alice', 90000, 'Engineering');
INSERT INTO employees VALUES (2, 'Bob', 75000, 'Marketing');
INSERT INTO employees VALUES (3, 'Carol', 85000, 'Engineering');

UPDATE employees
SET salary = salary * 1.05
WHERE department = 'Engineering';

SELECT name, salary FROM employees WHERE department = 'Engineering';
-- Alice|94500.0
-- Carol|89250.0

```

### Update using a join with FROM

```
CREATE TABLE departments (name TEXT PRIMARY KEY, budget REAL);
INSERT INTO departments VALUES ('Engineering', 500000);
INSERT INTO departments VALUES ('Marketing', 200000);

CREATE TABLE employees2 (id INTEGER PRIMARY KEY, name TEXT, bonus REAL, dept TEXT);
INSERT INTO employees2 VALUES (1, 'Alice', 0, 'Engineering');
INSERT INTO employees2 VALUES (2, 'Bob', 0, 'Marketing');

UPDATE employees2
SET bonus = departments.budget * 0.01
FROM departments
WHERE employees2.dept = departments.name;

SELECT name, bonus FROM employees2;
-- Alice|5000.0
-- Bob|2000.0

```
