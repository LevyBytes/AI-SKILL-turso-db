# Insert select and default values

[Context index](explain-insert-and-replace-reference.md) · [Setup and preceding context](insert-syntax-and-row-values.md) · [Continue](insert-conflicts-and-returning.md)

### INSERT INTO … SELECT

 Copies rows produced by a SELECT statement. The SELECT result column count has to equal the target-column count.

```
INSERT INTO archive_employees (name, department, salary)
SELECT name, department, salary
FROM employees
WHERE termination_date IS NOT NULL;

```

 A SELECT used here may use any valid clause, including WHERE, JOIN, GROUP BY, ORDER BY, LIMIT, UNION, or subqueries.

```
INSERT INTO monthly_summary (department, headcount, total_salary)
SELECT department, COUNT(*), SUM(salary)
FROM employees
GROUP BY department;

```

### DEFAULT VALUES

 Creates one row with defaults assigned to every column. Columns lacking an explicit DEFAULT definition become NULL, and INTEGER PRIMARY KEY columns get an automatically generated rowid.

```
CREATE TABLE audit_log (
  id INTEGER PRIMARY KEY,
  created_at TEXT DEFAULT (datetime('now')),
  action TEXT DEFAULT 'unknown'
);

INSERT INTO audit_log DEFAULT VALUES;
-- Inserts: (auto_id, current_timestamp, 'unknown')

```

### Column Default Values

 With a column list, omitted columns fall back to their default values.

```
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY,
  title TEXT NOT NULL,
  status TEXT DEFAULT 'pending',
  priority INTEGER DEFAULT 0,
  created_at TEXT DEFAULT (datetime('now'))
);

-- status, priority, and created_at use their defaults
INSERT INTO tasks (title) VALUES ('Review pull request');

```

### DEFAULT Keyword in VALUES

 **Turso extension** - This behavior matches SQL:2016 in the SQL standard, but SQLite does not implement it. In a VALUES list, the `DEFAULT` keyword may stand wherever a value expression would appear. It evaluates to the default declared for that column in the CREATE TABLE statement, or to NULL when no default exists. This helps with multi-row INSERT statements where defaults are needed in different columns on different rows, a case that column omission alone cannot express.

```
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY,
  title TEXT NOT NULL,
  status TEXT DEFAULT 'pending',
  priority INTEGER DEFAULT 0
);

-- Use DEFAULT for specific columns
INSERT INTO tasks (id, title, status, priority)
VALUES (1, 'Fix bug', DEFAULT, 5);
-- Inserts: (1, 'Fix bug', 'pending', 5)

-- Different columns use DEFAULT in different rows
INSERT INTO tasks (id, title, status, priority) VALUES
  (2, 'Write docs', DEFAULT, 3),
  (3, 'Code review', 'in_progress', DEFAULT);
-- Inserts: (2, 'Write docs', 'pending', 3)
--          (3, 'Code review', 'in_progress', 0)

```

 The `DEFAULT` keyword may appear only in INSERT VALUES lists. Placing it in SELECT, WHERE, or UPDATE contexts raises an error.
