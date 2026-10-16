# Delete returning and row limits

[Context index](virtual-tables-delete-and-drop-reference.md) · [Setup and preceding context](delete-syntax-filtering-and-ordering.md)

### Delete with RETURNING

```
CREATE TABLE queue (id INTEGER PRIMARY KEY, payload TEXT, priority INTEGER);
INSERT INTO queue VALUES (1, 'job_a', 10);
INSERT INTO queue VALUES (2, 'job_b', 5);
INSERT INTO queue VALUES (3, 'job_c', 10);

DELETE FROM queue
WHERE id = (SELECT id FROM queue ORDER BY priority DESC, id ASC LIMIT 1)
RETURNING id, payload;
-- 1|job_a

```

### Delete the oldest rows with ORDER BY and LIMIT

```
CREATE TABLE events (id INTEGER PRIMARY KEY, message TEXT, created_at TEXT);
INSERT INTO events VALUES (1, 'startup', '2025-01-01');
INSERT INTO events VALUES (2, 'request', '2025-01-02');
INSERT INTO events VALUES (3, 'shutdown', '2025-01-03');
INSERT INTO events VALUES (4, 'startup', '2025-01-04');

DELETE FROM events
ORDER BY created_at ASC
LIMIT 2;

SELECT * FROM events;
-- 3|shutdown|2025-01-03
-- 4|startup|2025-01-04

```

### See Also

- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) when changing rows that already exist
- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) when adding rows to a table
- [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table) when the entire table should be removed
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when reading data
