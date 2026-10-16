# View definition and query examples

[Context index](custom-types-and-view-reference.md)

## CREATE VIEW

 Define a named view around a SELECT statement.

### Syntax

```
CREATE [TEMPORARY] VIEW [IF NOT EXISTS] [schema-name.]view-name
    AS select-statement;

```

### Description

 A view is a virtual table specified by a SELECT statement. It stores no data on disk; when queried, Turso runs the underlying SELECT. Views can simplify complex queries, keep a stable interface as table schemas change, and limit the visible rows or columns.

### Parameters

| Parameter | Description |
| --- | --- |
| `TEMPORARY` | Creates the view in a temporary database. Only the current connection can see it, and it is dropped when that connection closes. `TEMP` is allowed as a synonym. |
| `IF NOT EXISTS` | Avoids an error when a view with the same name already exists. If the view is already present, the statement performs no action. |
| `schema-name` | Names the attached database where the view should be created. If omitted, the main database is used by default. This cannot be combined with `TEMPORARY`. |
| `view-name` | A database-unique name assigned to the view. |
| `select-statement` | The SELECT query used as the view definition. |

### Behavior

- Views are read-only. INSERT, UPDATE, and DELETE against a view are unsupported unless the view has an INSTEAD OF trigger.
- Turso stores the SELECT statement in `sqlite_schema` and parses it whenever the view is referenced.
- Dropping a referenced table or altering it incompatibly causes later queries against the view to error.

### Examples

### Simplify a Common Query

```
CREATE VIEW active_users AS
SELECT id, name, email
FROM users
WHERE status = 'active';

-- Use the view like a table
SELECT * FROM active_users WHERE name LIKE 'A%';

```

### Aggregate View

```
CREATE VIEW department_stats AS
SELECT
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

SELECT * FROM department_stats ORDER BY avg_salary DESC;

```

### Temporary View for a Session

```
CREATE TEMP VIEW my_tasks AS
SELECT id, title, due_date
FROM tasks
WHERE assignee = 'alice';

```

### See Also

- CREATE MATERIALIZED VIEW: [materialized view reference](https://docs.turso.tech/sql-reference/statements/create-materialized-view) covers views that keep precomputed results on disk
- [DROP VIEW](https://docs.turso.tech/sql-reference/statements/drop-view) explains view removal
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) documents the query syntax used by view definitions
- [CREATE TRIGGER](https://docs.turso.tech/sql-reference/statements/create-trigger) covers INSTEAD OF triggers that make views writable
