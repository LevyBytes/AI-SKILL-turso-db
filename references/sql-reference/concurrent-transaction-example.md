# Concurrent transaction example

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](transaction-begin-commit-and-rollback.md) · [Previous phase](transaction-lifecycle-and-concurrent-writes.md)

### Example

```
-- Connection 1
PRAGMA journal_mode = mvcc;
BEGIN CONCURRENT;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Connection 2 (running simultaneously)
PRAGMA journal_mode = mvcc;
BEGIN CONCURRENT;
UPDATE accounts SET balance = balance + 50 WHERE id = 2;
COMMIT;
-- Both succeed because they modified different rows

```

 When both connections modify the same row, the second COMMIT fails:

```
-- Connection 1
BEGIN CONCURRENT;
UPDATE accounts SET balance = 900 WHERE id = 1;
COMMIT;  -- succeeds

-- Connection 2 (started before Connection 1 committed)
BEGIN CONCURRENT;
UPDATE accounts SET balance = 800 WHERE id = 1;
COMMIT;  -- fails: write-write conflict detected
ROLLBACK;
-- Retry the transaction from BEGIN CONCURRENT

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) covers inserting rows
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) covers updating rows
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) covers deleting rows
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) covers differences from SQLite
