# Transaction begin commit and rollback

[Context index](select-and-transaction-reference.md) · [Continue](transaction-lifecycle-and-concurrent-writes.md)

## Transactions

 Transactions combine multiple SQL statements into one atomic unit of work. COMMIT makes the transaction changes visible together; ROLLBACK undoes them. A statement error does not necessarily roll back earlier statements, so the caller must stop and roll back when the complete operation cannot succeed. Turso supports explicit transactions using BEGIN, COMMIT, and ROLLBACK, and each standalone statement also runs inside an implicit transaction (autocommit mode).

### Syntax

```
BEGIN [DEFERRED | IMMEDIATE | EXCLUSIVE] [TRANSACTION];

COMMIT [TRANSACTION];
END [TRANSACTION];

ROLLBACK [TRANSACTION];

```

### BEGIN

 BEGIN starts a new explicit transaction. After it is open, every following statement belongs to that transaction until COMMIT or ROLLBACK is issued.

### Transaction Types

| Type | Description |
| --- | --- |
| `DEFERRED` | Default. Locks are not acquired until the first read or write operation. Reads acquire a shared lock; writes acquire a write lock. |
| `IMMEDIATE` | Gets a write lock as soon as BEGIN executes. Other connections may continue reading, but they cannot write until the transaction completes. |
| `EXCLUSIVE` | In Turso, this behaves the same as IMMEDIATE. Turso always uses WAL mode, which makes EXCLUSIVE and IMMEDIATE equivalent. |

 When no type is specified, the transaction uses `DEFERRED` by default.

```
-- Start a deferred transaction (default)
BEGIN;

-- Start an immediate transaction
BEGIN IMMEDIATE;

-- The TRANSACTION keyword is optional
BEGIN IMMEDIATE TRANSACTION;

```

### COMMIT / END

 COMMIT, or its END alias, finalizes the transaction and writes every change to the database. After commit, changes are durable and visible to other connections.

```
BEGIN;
INSERT INTO accounts (name, balance) VALUES ('Alice', 1000);
INSERT INTO accounts (name, balance) VALUES ('Bob', 500);
COMMIT;

```

 COMMIT and END are interchangeable.

```
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE name = 'Alice';
END;

```

### ROLLBACK

 ROLLBACK aborts the active transaction and undoes every change made since BEGIN. The database returns to the state it had before that transaction began.

```
BEGIN;
DELETE FROM accounts WHERE balance < 0;
-- Changed our mind, undo everything
ROLLBACK;

```
